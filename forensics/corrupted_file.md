
### 🛠 Quick Commands to Fix Corrupted JPEG (CTF)

**1️⃣ Check file type**

```bash
file file
```

**2️⃣ Inspect header**

```bash
hexdump -C file | head
```

**3️⃣ Fix JPEG magic bytes**

```bash
printf '\xff\xd8' | dd of=file bs=1 seek=0 count=2 conv=notrunc
```

**4️⃣ Verify**

```bash
file file
```

**5️⃣ Open image**

```bash
xdg-open file
```

**6️⃣ Search flag**

```bash
strings file | grep -i flag
```

---

### 📌 Super Short Version (Best for Notes)

```bash
hexdump -C file | head
printf '\xff\xd8' | dd of=file bs=1 seek=0 count=2 conv=notrunc
file file
xdg-open file
```

---

### 💡 Tip for CTF

Common **file magic headers** you may need to fix:

| File | Header        |
| ---- | ------------- |
| JPEG | `FF D8 FF`    |
| PNG  | `89 50 4E 47` |
| GIF  | `47 49 46 38` |
| PDF  | `25 50 44 46` |
| ZIP  | `50 4B 03 04` |

