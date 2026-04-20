# Investigation Encoded 1

## Challenge Overview

We are given a binary called `mystery` and an `output` file. The goal is to recover the original flag that was encoded into `output`.

---

## Step 1: Initial Recon

First, check what the `output` file actually is:

```bash
file output
```

Result: `Non-ISO extended-ASCII text, with no line terminators` — not an image or plaintext flag.

Inspect the raw bytes:

```bash
xxd -g 1 output
```

```
00000000: 8e 8e ba 3b b8 ea 23 a8  a8 ba 3a 8e ee 38 ee a3  ...;..#...:..8..
00000010: a8 eb 8a a3 ab 80                                  ......
```

The output is short and clearly encoded — not human-readable.

---

## Step 2: Running the Binary

```bash
./mystery
```

Output: `./flag.txt not found`

Create a fake flag (no newline, no underscore — we'll learn why shortly):

```bash
echo -n picoCTF{fakeflag} > flag.txt
./mystery
```

Output: `Error, I don't know why I crashed.`

This tells us the binary validates its input. We need to understand the encoding scheme.

---

## Step 3: Static Analysis with Ghidra

Decompile the binary in Ghidra. Three key functions emerge:

### `main()`

- Opens `flag.txt` and reads it into a global `flag` buffer
- Checks file size is ≤ 65535 bytes
- Sets global state: `flag_index`, `buffChar = 0`, `remain = 7`
- Calls `encode()`, writes result to `output`

### `isValid()`

Only three character classes are accepted:
- Lowercase `a–z`
- Uppercase `A–Z`
- Space (` `)

This is why `echo` (which appends `\n`) and underscores caused crashes.

### `encode()`

For each character in the flag:
1. Convert to lowercase via `lower()`
2. Space is remapped to `{` — `if (local_9 == ' ') local_9 = '{'`
3. Look up the character in a global `matrix`:
   - `start = matrix[(ch - 'a') * 8]`
   - `length = matrix[(ch - 'a') * 8 + 4]`
4. For each index from `start` to `start + length`, call `getValue(index)` and `save()` the resulting bit

### `getValue()`

Reads a single bit from the global `secret` byte array:

```c
return (secret[param >> 3] >> (7 - (param & 7))) & 1;
```

This is standard big-endian bit extraction: byte index = `param / 8`, bit position = `7 - (param % 8)`.

### `save()`

Accumulates bits into `buffChar` and writes a byte to `output` every 7 bits (not 8 — `remain` starts at 7). This is the unusual packing scheme:

```c
buffChar = buffChar | param_1;
if (remain == 0) {
    fputc(buffChar, output);
    buffChar = 0; remain = 7;
} else {
    buffChar = buffChar * 2;  // left-shift by 1
    remain--;
}
```

---

## Step 4: Extracting the Data with Radare2

Use `r2` to dump the two global variables the binary uses:

```bash
r2 -AA mystery
```

### Dump `secret` (37 bytes):

```
[0x00001180]> pc @ obj.secret
```

```c
{0xb8,0xea,0x8e,0xba,0x3a,0x88,0xae,0x8e,0xe8,0xaa,0x28,0xbb,0xb8,0xeb,
 0x8b,0xa8,0xee,0x3a,0x3b,0xb8,0xbb,0xa3,0xba,0xe2,0xe8,0xa8,0xe2,0xb8,
 0xab,0x8b,0xb8,0xea,0xe3,0xae,0xe3,0xba,0x80}
```

### Dump `matrix` (27 pairs of uint32 = 54 words):

```
[0x00001180]> pcw @ obj.matrix
```

Each pair is `(length, start)` for letters `a` through `z` plus `{` (space):

| Letter | Start (bit index) | Length (bits) |
|--------|-------------------|---------------|
| a | 0x000 | 8 |
| b | 0x008 | 12 |
| c | 0x014 | 14 |
| d | 0x022 | 10 |
| e | 0x02c | 4 |
| f | 0x030 | 12 |
| ... | ... | ... |
| z | 0x116 | 4 |
| { (space) | 0x124 | — |

> **Important note on little-endian multi-byte values:** For letters near the end of the alphabet (`y`, `z`), the start indices exceed 0xFF. In the raw byte dump these appear as two adjacent non-zero bytes (e.g. `0x06, 0x01`). These must be combined right-to-left (little-endian): `0x0106 = 262`. Failing to do this gives wrong bit sequences for those letters.

---

## Step 5: Building the Decoder

The encoding is essentially a **variable-length prefix code** (similar to Huffman coding). Each letter maps to a unique bit sequence drawn from `secret`.

### Rebuild the dictionary

For each character `ch` in `abcdefghijklmnopqrstuvwxyz{`:
- Look up `start` and `length` from `matrix`
- Extract `length` bits from `secret` starting at bit `start`
- Store `bits → ch` in a lookup dictionary

### Get the output hex

```bash
xxd -c1 output | cut -d" " -f2 | tr -d "\n"
```

Result: `8e8eba3bb8ea23a8eee2e3ae8eea3ba8e8ab8e8ae3bb80`

### Decode greedily

Convert output bytes to a flat bit string. Walk bit by bit, accumulating into a buffer. When the buffer matches a dictionary entry, emit that character (mapping `{` back to space) and clear the buffer.

---

## Step 6: The Flag

Running the decoder against the output hex yields the decoded flag. The `picoCTF{...}` wrapper can be inferred since the binary strips punctuation — the decoded text is the inner content of the flag.

---

## Key Takeaways

| Concept | Detail |
|--------|--------|
| Encoding type | Variable-length bit prefix code (Huffman-like) |
| Bit packing | 7-bits-per-byte (not standard 8) via `remain = 7` |
| Space encoding | Remapped to `{` before lookup |
| Valid chars | `a–z`, `A–Z`, space only |
| Data extraction | Radare2 `pc` / `pcw` commands on global symbols |
| Little-endian caveat | Multi-byte matrix values must be combined correctly |
