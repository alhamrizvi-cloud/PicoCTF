

## 🧠 Idea of the Challenge

* You are given a `.bmp` image.
* It looks normal, but **data is hidden inside it (steganography)**.
* The goal is to find the hidden **flag**.


## 🔍 Step 1: Check the file in hex

Run:

```bash
xxd -g 1 -l 250 output.bmp
```

👉 While looking at hex, we see:

```
50 4b
```

This is important because:

* `50 4b` = **"PK"**
* This is the signature of a **ZIP file**


## 🤔 Problem

Normally ZIP starts with:

```
50 4b 03 04
```

But here we see:

```
50 4b 95 52 03 04
```

👉 That means:

* ZIP signature is **broken/split**
* So the file is **intentionally corrupted**


## 🔎 Step 2: Find all "PK"

Run:

```bash
binwalk -R "\x50\x4b" output.bmp
```

👉 You will see multiple locations, but none form a proper ZIP.

So:

* Data is there
* But it's **scrambled**

---

## 💡 Step 3: Understand the trick

Looking closely:

* Bytes are **interleaved (mixed)**
* Real data is hidden by inserting junk bytes

👉 Solution:
We must **remove extra bytes**


## ⚙️ Step 4: Convert file to hex bytes

```bash
xxd -p -c 1 output.bmp > hex
```

Now file looks like:

```
42
4d
8a
a4
...
```

## 🧑‍💻 Step 5: Fix the data (Python script)

```python
bytes = []

fileObj = open("hex", "r")
bytes = fileObj.read().split()
fileObj.close()

p1 = bytes[0::4]
p2 = bytes[1::4]

zipstring = ""

for f, b in zip(p1, p2):
    zipstring += f
    zipstring += b

print(zipstring)
```

👉 What this does:

* Takes every **real byte**
* Skips fake ones
* Rebuilds original file

---

## 🔁 Step 6: Convert back to binary

```bash
python3 truncate_hex.py | xxd -r -p > hex_trunc
```

---

## 🔍 Step 7: Check fixed file

```bash
xxd hex_trunc
```

Now you should see:

```
50 4b 03 04
```

👉 ZIP is now **correct**

---

## 📦 Step 8: Extract hidden ZIP

```bash
binwalk -e hex_trunc
```

👉 It extracts:

```
ZnJhbmtlbnN0ZWluLXRlc3QudHh0
```

---

## 📖 Step 9: Read file

```bash
cat ZnJhbmtlbnN0ZWluLXRlc3QudHh0
```

👉 It contains text from **Frankenstein**

---

## 🚩 Step 10: Find the flag

```bash
grep picoCTF ZnJhbm..................
```

👉 Output:

```
picoCTF{REDACTED}
```


## 🔑 Simple Summary

* BMP had **hidden ZIP**
* ZIP was **broken by mixing bytes**
* You:

  1. Found `PK`
  2. Fixed byte order
  3. Rebuilt ZIP
  4. Extracted file
  5. Found flag
