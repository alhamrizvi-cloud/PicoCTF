

I started by analyzing the binary and understanding how it processes the image and the flagon ghidra


##  Understanding the Code

The program opens three files:

```c
fopen("flag.txt","r");
fopen("original.bmp","r");
fopen("encoded.bmp","a");
```

* `flag.txt` → contains the flag
* `original.bmp` → clean image
* `encoded.bmp` → output image with hidden data

---

### Step 1: Copy original image

```c
for (i = 0; i < 2000; i++)
    fputc(byte, encoded.bmp);
```

 The first **2000 bytes** are copied directly.
This is usually the **BMP header + initial data**, so nothing suspicious here.

---

### Step 2: Read the flag

```c
fread(flag, 50, 1, flag.txt);
```

Reads exactly **50 characters**
If not 50 → program exits

---

### Step 3: Encode the flag

```c
for each character:
    for 8 times:
        encoded_byte = codedChar(...)
        write to image
```

Key observations:

* Each character → **8 bytes**
* So total hidden data = **50 × 8 = 400 bytes**
* Each byte stores **1 bit of the flag**

 This is **LSB steganography**

### Important transformation

```c
flag_char - 5
```

 Before encoding, each character is shifted by **-5**
So during extraction, we must do **+5**

### Step 4: Copy rest of image

After embedding, the program copies the remaining bytes normally.

## Exploitation Strategy

Instead of reversing `codedChar()` fully, I focused on the pattern:

* After offset **2000**, data looks like:

  ```
  e8 e9 e8 e9 ...
  ```
* These differ only in the **last bit**

So:

* `e8 → 0`
* `e9 → 1`

 That means: **LSB = hidden bit**

## ⚡ Extraction Steps

### 1. Extract hidden region

```bash
dd if=encoded.bmp of=flag_bytes.bin bs=1 skip=2000 count=400
```

### 2. Decode using Python

```python
data = open("flag_bytes.bin", "rb").read()

bits = [str(b & 1) for b in data]

flag = ""
for i in range(0, len(bits), 8):
    byte = bits[i:i+8][::-1]   # reverse bit order
    c = int("".join(byte), 2) + 5
    flag += chr(c)

print(flag)
```

## Final Flag

```text
picoCTF{n3xt_0n30000000000000000000000000e1176fc3}
```

## Key Takeaways

* Data hidden using **LSB steganography**
* Offset matters → skip first 2000 bytes
* Each char = 8 bytes
* Bit order was reversed
* Characters were shifted by `-5`

If you want, I can show how to **recognize LSB stego instantly just by looking at hex (like you did with e8/e9)** — that’s a really strong skill for CTFs.
