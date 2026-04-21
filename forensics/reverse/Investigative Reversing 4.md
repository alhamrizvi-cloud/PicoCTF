
I opened the binary in **Ghidra** and started from `main`.

## Ghidra Analysis

The `main` function is very small:

```c id="a1c2d3"
flag = local_48;
flag_index = &local_4c;

fopen("flag.txt","r");
fread(flag, 0x32, 1, file);

encodeAll();
```

### What this tells us

* The program reads **50 bytes of the flag**
* Stores it in a global pointer `flag`
* Uses `flag_index` to keep track of position
* Then calls `encodeAll()` → all real logic is there

## Inside `encodeAll()` (key idea)

From reversing, the behavior is:

* There are **5 output images**
* The program loops over the flag
* For each character:

  * It encodes it using **LSB (least significant bit)**
  * Each character is split into **8 bits**
* But instead of putting everything in one image:

  * It distributes data across **5 images**
  * And **skips bytes in between**

### Important pattern

For each image:

* Only when `(j % 5 == 0)` → actual flag bits are stored
* Otherwise → normal image bytes are copied

So layout becomes:

```text id="p1x1"
[8 bytes of encoded bits] → [skip 4 bytes] → repeat
```

And this pattern is spread across **5 images in reverse order**

## Key Insight

* Flag is encoded using **LSB steganography**
* Each character → 8 bits → stored across 8 bytes
* Data is:

  * **Split across 5 images**
  * **Interleaved with junk bytes**
  * **Stored starting at offset 2019**

## Exploit Script

Here is the full script you used:

```python id="exploit123"
from pwn import unbits

bin_str = ""
for i in range(5, 0, -1):
    with open("Item0{}_cp.bmp".format(i), "rb") as b:
        b.seek(2019)

        for j in range(50):
            if (j % 5 == 0):
                for k in range(8):
                    byte = ord(b.read(1))
                    bit = byte & 1
                    bin_str += str(bit)
            else:
                b.read(1)

char_str = unbits(bin_str, endian='little')
print("Flag: {}".format(char_str.decode("ascii")))
```

## What the Script Does

### Step by step

### 1. Loop through images

```python id="s1"
for i in range(5, 0, -1):
```

* Reads images in **reverse order**
* Important because encoding used same order

### 2. Jump to encoded region

```python id="s2"
b.seek(2019)
```

* Skip header + normal data
* Go directly to hidden section

### 3. Loop through flag structure

```python id="s3"
for j in range(50):
```

* Total 50 positions (flag length logic)

### 4. Extract only real data

```python id="s4"
if (j % 5 == 0):
```

* Only every 5th iteration contains **actual flag bits**
* Others are junk → skipped

### 5. Read 8 bits per character

```python id="s5"
for k in range(8):
    byte = ord(b.read(1))
    bit = byte & 1
```

* Take **LSB of each byte**
* Build binary string

### 6. Skip junk bytes

```python id="s6"
else:
    b.read(1)
```

* Ignore non-encoded data

### 7. Convert bits → characters

```python id="s7"
char_str = unbits(bin_str, endian='little')
```

* Groups bits into bytes
* Converts to ASCII

### 8. Print flag

```python id="s8"
print(char_str.decode("ascii"))
```

```text id="flagfinal"
picoCTF{N1c3_R3ver51ng_5k1115_00000000000e17ba9e6}
```

## Summary

This challenge combines multiple tricks:

* LSB steganography
* Multi-file distribution
* Byte skipping (noise insertion)
* Reverse order reconstruction

The main difficulty is not decoding — it is **understanding how data is split and where it is hidden**.
