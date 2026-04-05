
```bash
gzip -d p.img.gz
```

This produced:

```
p.img
```

---

## 📂 Step 2: Generate File Listing

We recursively listed all files in the image:

```bash
fls -r -m / p.img > body.txt
```

This creates a body file containing filesystem metadata.

---

## ⏱️ Step 3: Build Timeline

We generated a timeline of file activity:

```bash
mactime -b body.txt -d > timeline.csv
```

Inspecting the timeline:

```bash
head timeline.csv
```

We notice something suspicious:

```
Tue Jan 01 1985 22:30:00 ... inode 4945 → /bin/bcab
```

👉 This stands out because:

* The timestamp is **very old (1985)** → likely manipulated or hidden
* The file is in `/bin/` → unusual location for hidden data

---

## 🔍 Step 4: Extract Suspicious File

We extract the file using its inode:

```bash
icat p.img 4945
```

Output:

```
NzFtMzExbjNfMHU3MTEzcl9oM3JfNDNhMmU3YWYK
```

---

## 🔐 Step 5: Decode the Data

The output appears to be **Base64 encoded**, so we decode it:

```bash
echo "NzFtMzExbjNfMHU3MTEzcl9oM3JfNDNhMmU3YWYK" | base64 -d
```

## 🎯 Final Output

```
picoCTF{...}
```

---

