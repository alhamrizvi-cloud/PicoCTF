
## Challenge Overview

We are given:

* A remote service that outputs an encrypted flag:

  ```
  235a201d702015483b1d412b265d3313501f0c072d135f0d2002302d57426752731306472e
  ```
* A binary: `hiddencipher`
* A file: `flag.txt` (fake / decoy)

Goal: **Recover the original flag**

---

## Step 1 – Initial Analysis

Running:

```bash
strings hiddencipher
```

We notice:

* `flag.txt`
* `"Here your encrypted flag:"`
* `%02x` → prints bytes in **hex format**

Also:

```
This file is packed with the UPX executable packer
```

---

## Step 2 – Unpacking the Binary

The binary is packed with UPX, so we unpack it:

```bash
upx -d hiddencipher
```

Now the binary is readable and can be properly reversed.

---

## Step 3 – Disassembly (GDB)

We inspect `main`:

```bash
gdb ./hiddencipher
disas main
```

### Key observations:

### 1. File Reading

```c
fp = fopen("flag.txt", "rb");
fseek(fp, 0, SEEK_END);
size = ftell(fp);
rewind(fp);
buffer = malloc(size + 1);
fread(buffer, 1, size, fp);
```

👉 The program reads the entire file into memory.

---

### 2. Secret Key

```c
secret = get_secret();
```

👉 The encryption key comes from `get_secret()`.

---

### 3. Core Encryption Loop

From assembly:

```asm
xor %esi, %eax
```

Which translates to:

```c
result = input_char ^ secret_char;
```

---

### 4. Modulo Trick

This block:

```asm
imul   $0x2aaaaaab
shr    $0x20
...
```

👉 Compiler optimization for:

```c
i % 6
```

---

## Final Reconstructed Logic

```c
for (int i = 0; i < size; i++) {
    result = buffer[i] ^ secret[i % 6];
    printf("%02x", result);
}
```

---

## Step 4 – Understanding the Cipher

This is a **repeating XOR cipher**:

```text
cipher[i] = flag[i] ^ key[i % 6]
```

We are given:

* Ciphertext (hex)
* We know flag format: `picoCTF{...}`

---

## Step 5 – Recovering the Key

Use known plaintext attack:

```python
cipher = bytes.fromhex("235a201d702015483b1d412b265d3313501f0c072d135f0d2002302d57426752731306472e")

known = b"picoCTF{"

key = [0]*6

for i in range(len(known)):
    key[i % 6] = cipher[i] ^ known[i]

key = bytes(key)
print("KEY:", key)
```

### Output:

```
KEY: b'S3Cr3t'
```

---

## Step 6 – Decrypting the Flag

```python
import itertools

cipher = bytes.fromhex("235a201d702015483b1d412b265d3313501f0c072d135f0d2002302d57426752731306472e")
key = b"S3Cr3t"

decoded = bytes([c ^ k for c, k in zip(cipher, itertools.cycle(key))])
print(decoded.decode())
```

---

## Final Flag

```
picoCTF{custom_xor_cipher_...}
```

---

## Key Takeaways

* Packed binaries often use UPX → always unpack first
* `%02x` in binaries → strong hint of byte-wise transformation
* Weird multiplication constants → compiler tricks for modulo
* XOR + repeating key → extremely common in CTFs
* Known plaintext (`picoCTF{`) makes key recovery trivial

---

## Bonus: Clean C Equivalent

```c
for (int i = 0; i < size; i++) {
    unsigned char result = buffer[i] ^ secret[i % 6];
    printf("%02x", result);
}
```
