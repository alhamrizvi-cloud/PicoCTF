# ✍️ Disk Forensics Writeup

The goal was to analyze a **disk image** and recover hidden or suspicious data using forensic tools.

---

## 🔧 Step 1: Extract the Disk Image

Initially, the disk image was compressed:

```bash
gzip -d partition4.img.gz
```

This decompresses the file and gives us:

```
partition4.img
```

---

## 📂 Step 2: Generate File Listing

We recursively list all files in the filesystem using:

```bash
fls -r -m / partition4.img > body.txt
```

* `-r` → recursive
* `-m /` → sets root directory
* Output saved to `body.txt`

---

## ⏱️ Step 3: Create Timeline

We generate a timeline of file activity:

```bash
mactime -b body.txt -d > timeline.csv
```

This gives us:

* File timestamps
* Metadata
* File paths

We inspect it:

```bash
head timeline.csv
```

This helps identify **suspicious files or activity patterns**.

---

## 🔍 Step 4: Locate Interesting File (Inode)

From the timeline or file listing, we identify a suspicious inode:

```
32716
```

---

## 📦 Step 5: Extract File Using `icat`

We extract the file content directly from the disk image:

```bash
icat partition4.img 32716
```

Output:

```
NTczNDE3aDEzcl83aDRuXzdoM18xNDU3XzU4NTI3YmIyMjIK
```

---

## 🔐 Step 6: Decode the Data

The output looks like **Base64**, so we decode it:

```bash
echo "NTczNDE3aDEzcl83aDRuXzdoM18xNDU3XzU4NTI3YmIyMjIK" | base64 -d
```

---

## 🎯 Final Output

```
picoCTF{...}
```

