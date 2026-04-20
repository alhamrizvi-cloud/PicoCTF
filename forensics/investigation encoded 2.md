# Investigation Encoded 2.md
We are given a binary called `no_auth_mistery` and an `output` file. The goal is to recover the original flag that was encoded into `output`. This is a follow-up to the previous `mystery` challenge, with several important differences.


## Step 1: Initial Recon

Run the binary:

```bash
./no_auth_mistery
```

Output: `Error: file ./flag.txt not found`

Create a fake flag and try again:

```bash
echo picoctf > flag.txt
./no_auth_mistery
```

Output: `Segmentation fault`

Unlike the previous challenge, this binary crashes even with a valid-looking flag. The reason is a `login()` function that runs before encoding and crashes without valid credentials. Since we only need to decode the `output` file statically, we can ignore this entirely.


## Step 2: Static Analysis with Ghidra

Decompile the binary. The structure is very similar to part 1, with three key differences.

### `main()`

- Initializes a global `badChars = '\0'`
- Opens and reads `flag.txt`
- Calls `login()` — **this causes the segfault**, skip it during debugging
- Sets globals: `flag_index`, `buffChar = 0`, `remain = 7`
- Calls `encode()`, writes result to `output`
- If `badChars == 1`, prints a corruption warning; otherwise prints done

### `isValid()` — expanded character set

Unlike part 1 (letters + space only), this binary also accepts **digits 0–9**. The encode function handles this with an explicit branch.

### `encode()` — three-stage character transform

```c
local_9 = lower(flag[*flag_index]);   // lowercase the character

if (local_9 == ' ') {
    local_9 = -0x7b;                  // space → 0x85 (char -123)
} else if ('/' < local_9 && local_9 < ':') {
    local_9 = local_9 + 'K';         // digit: add 75
}

local_9 = local_9 - 0x61;            // subtract 'a' (97)

if (local_9 < 0 || local_9 > 0x24) {
    badChars = 1;                     // out of range = invalid
}

if (local_9 != 0x24) {               // 0x24 = 36 = space, skip rotation
    iVar2 = ((int)local_9 + 0x12) % 0x24;
    bVar1 = (byte)(iVar2 >> 0x1f);
    local_9 = ((byte)iVar2 ^ bVar1) - bVar1;  // rotate index by 18 mod 36
}
```

**Breaking down the character mapping:**

| Input | After `+75` | After `−97` | Raw index |
|-------|-------------|-------------|-----------|
| `a`–`z` | unchanged | 0–25 | 0–25 |
| `0`–`9` | `0`+75=123, etc. | 26–35 | 26–35 |
| space | → 0x85 (−123) | −123−97=−220... wait, 0x85−0x61=0x24=36 | 36 |

After computing the raw index, a **rotation of +18 mod 36** is applied (for non-space characters) before using it to look up `indexTable`. This scrambles which bit sequence corresponds to which character.

### `getValue()` and `save()`

Identical to part 1:
- `getValue(i)` extracts bit `i` from the global `secret` array (big-endian bit order)
- `save(bit)` packs bits into bytes 7-at-a-time and writes to `output`

## Step 3: Extracting Data with Radare2

```bash
r2 -AA no_auth_mistery
```

### Dump `secret` (71 bytes):

```
[0x00000ae0]> pc @ obj.secret
```

```c
{0x8b,0xaa,0x2e,0xee,0xe8,0xbb,0xae,0x8e,0xbb,0xae,0x3a,0xee,0x8e,0xee,
 0xa8,0xee,0xae,0xe3,0xaa,0xe3,0xae,0xbb,0x8b,0xae,0xb8,0xea,0xae,0x2e,
 0xba,0x2e,0xae,0x8a,0xee,0xa3,0xab,0xa3,0xbb,0xbb,0x8b,0xbb,0xb8,0xae,
 0xee,0x2a,0xee,0x2e,0x2a,0xb8,0xaa,0x8e,0xaa,0x3b,0xaa,0x3b,0xba,0x8e,
 0xa8,0xeb,0xa3,0xa8,0xaa,0x28,0xbb,0xb8,0xae,0x2a,0xe2,0xee,0x3a,0xb8,0x00}
```

### Dump `indexTable` (38 uint32 values):

```
[0x00000ae0]> pcw @ obj.indexTable
```

```c
{0x000,0x004,0x012,0x028,0x03c,0x052,0x064,0x078,0x08e,0x09e,
 0x0b4,0x0c8,0x0da,0x0ea,0x0fc,0x10e,0x11e,0x134,0x148,0x15a,
 0x16a,0x172,0x180,0x18c,0x19a,0x1aa,0x1bc,0x1c8,0x1d6,0x1e0,
 0x1ea,0x1f0,0x200,0x20a,0x216,0x222,0x230,0x234}
```

**Key difference from part 1:** `indexTable` is a flat array of 38 cumulative bit positions. The bit sequence for symbol `i` runs from `indexTable[i]` to `indexTable[i+1]`. There are no separate length/start pairs — the length is implicit: `length = indexTable[i+1] - indexTable[i]`.

### Get the output hex:

```bash
xxd -c1 output | cut -d" " -f2 | tr -d "\n"
```

Result:
```
baa3aebb8a3aab8eaa3aebb8ea8eaae2eae8eab8eab8eab8eab8eab8eab8eab8eab8eab8eab8eab8baba2bba8eaae3aa8bbbb8bbbb8bbbb8b8
```

---

## Step 4: Building the Decoder

### Step 4a: Rebuild the encoding dictionary

For each symbol index 0–36, extract its bit sequence from `secret` using `indexTable`:

```python
def get_bit(secret, i):
    return (secret[i >> 3] >> (7 - (i & 7))) & 1

dict = {}
for sym in range(37):
    start = index_table[sym]
    end   = index_table[sym + 1]
    bits  = ''.join(str(get_bit(secret, j)) for j in range(start, end))
    dict[bits] = sym
```

### Step 4b: Reverse the rotation to recover characters

The encoder applied `rotated = (raw + 18) % 36` before lookup. To reverse:

```python
def sym_to_char(sym):
    if sym == 36: return ' '
    raw = (sym - 18) % 36        # reverse the +18 rotation
    if raw < 26: return chr(raw + ord('a'))
    return chr(raw - 26 + ord('0'))
```

### Step 4c: Greedy bit-stream decode

Convert output bytes to a flat bit string. Walk bit by bit — when the buffer matches a dictionary entry, emit the character and clear the buffer:

```python
flag_encoded = bin(0xbaa3aebb8a3aab8eaa3aebb8ea8eaae2eae8eab8eab8eab8eab8eab8eab8eab8eab8eab8eab8eab8baba2bba8eaae3aa8bbbb8bbbb8bbbb8b8)[2:]

buffer = ""
flag = ""
for bit in flag_encoded:
    buffer += bit
    if buffer in decoded_dict:
        flag += decoded_dict[buffer]
        buffer = ""

print("picoCTF{{{}}}".format(flag))
```

---

## Step 5: The Flag

```
picoCTF{t1m3f1i3500000000000563f999c}
```

## Key Differences from Part 1

| Feature | Part 1 (`mystery`) | Part 2 (`no_auth_mistery`) |
|---|---|---|
| Lookup structure | `matrix`: `(length, start)` pairs | `indexTable`: flat cumulative positions |
| Valid characters | `a–z`, space | `a–z`, `0–9`, space |
| Character transform | None | `+18 mod 36` rotation applied before lookup |
| Anti-debugging | Binary crashes on bad flag chars | `login()` causes segfault before encoding |
| `secret` size | 37 bytes | 71 bytes |
| Digit encoding | Not supported | `digit + 75 − 97` maps `0–9` → indices 26–35 |

## Lessons Learned

- **`indexTable` as cumulative positions** is cleaner than part 1's paired `(start, length)` matrix — use `indexTable[i+1] - indexTable[i]` for the length implicitly.
- **Character rotation** (`+18 mod 36`) is a simple substitution cipher layered on top of the variable-length encoding. Always check for pre-lookup transforms in `encode()`.
- **The output hex must be exact.** A single wrong byte produces a completely different (and wrong) decode. Always use `xxd -c1 output | cut -d" " -f2 | tr -d "\n"` to get the canonical hex.
- **`login()` can be ignored** for static decoding — we never need to run the binary.
