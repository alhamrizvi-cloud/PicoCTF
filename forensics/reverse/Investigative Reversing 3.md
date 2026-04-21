

I opened the binary in **Ghidra** and focused on the `main` function. The logic looked very similar to the previous part, but with a small twist.

First, the program opens three files: `flag.txt`, `original.bmp`, and `encoded.bmp`. Then it copies the beginning of the original image directly into the output file. This time, the number of bytes copied is `0x2d3` (723 bytes), which becomes the new offset where the hidden data starts.

After that, the program reads 50 bytes from `flag.txt`. This confirms the flag length is fixed.

The important part is the encoding loop:

```c
for (i = 0; i < 100; i++) {
    if (i % 2 == 0) {
        // encode flag byte
    } else {
        // copy original byte
    }
}
```

From this, it’s clear that:

* Only even iterations actually encode data
* Odd iterations just copy normal image bytes

Inside the encoding block, the function `codedChar()` is used, just like before. It runs 8 times for each character, which means each flag character is stored bit by bit across 8 bytes. This indicates LSB steganography again.

The index used is `flag[i / 2]`, which means:

* i = 0 → flag[0]
* i = 2 → flag[1]
* i = 4 → flag[2]

So only half the loop iterations actually consume the flag.

This creates a pattern in the output file:

* 8 bytes of encoded data
* 1 normal byte (junk)
* repeat

So unlike the previous challenge, the hidden data is **interleaved with normal bytes**.

---

## Exploitation

Instead of reversing `codedChar()` fully, I focused on the byte pattern in the encoded image.

After offset 723, I extracted the data:

```bash
dd if=encoded.bmp of=data.bin bs=1 skip=723
```

From the structure, I knew:

* Every 9 bytes → 8 bytes useful + 1 junk byte
* Each group of 8 bytes → 1 character

So I wrote a simple script:

* Read 8 bytes
* Extract LSB from each byte
* Reverse bit order
* Convert to character
* Skip 1 byte
* Repeat

The script reconstructs the flag directly.

---

## Final Result

```
picoCTF{4n0th3r_L5b_pr0bl3m_000000000000012cceb8f}
```

btw here's the script i have used ai lol,

```
data = open("data.bin", "rb").read()

i = 0
flag = ""

while len(flag) < 50:
    chunk = data[i:i+8]   # encoded
    bits = [(b & 1) for b in chunk]

    bits = bits[::-1]     # reverse if needed
    c = int("".join(map(str,bits)), 2)

    flag += chr(c)
    i += 9   # 8 used + 1 skipped

print(flag)
```
