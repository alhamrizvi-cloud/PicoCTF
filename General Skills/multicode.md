
```text
NjM3NjcwNjI1MDQ3NTMyNTM3NDI2MTcyNjY2NzcyNzE1ZjcyNjE3MDMwNzE3NjYxNzQ1ZjM4MzQzODZlMzQzNjM2NmYyNTM3NDQ=
```

---

## Step 1 – Base64 Decoding

The string is Base64 encoded.

```bash
echo "NjM3NjcwNjI1MDQ3NTMyNTM3NDI2MTcyNjY2NzcyNzE1ZjcyNjE3MDMwNzE3NjYxNzQ1ZjM4MzQzODZlMzQzNjM2NmYyNTM3NDQ=" | base64 -d
```

Output:

```text
637670625047532537426172666677715f72617030717661745f3834386e3436366f253744
```

---

## Step 2 – Hex Decoding

The result is a hex string. Convert it to ASCII:

```bash
echo "637670625047532537426172666677715f72617030717661745f3834386e3436366f253744" | xxd -r -p
```

Output:

```text
cvpbPGS%7Barffwq_rap0qvat_848n466o%7D
```

---

## Step 3 – URL Decoding

The string contains URL encoding:

* `%7B` → `{`
* `%7D` → `}`

Decoded:

```text
cvpbPGS{arffwq_rap0qvat_848n466o}
```

---

## Step 4 – ROT13 Decoding

The remaining text is encoded with ROT13. Decode using:

```bash
echo "cvpbPGS{arffwq_rap0qvat_848n466o}" | tr 'a-zA-Z' 'n-za-mN-ZA-M'
```

---

## Final Flag

```text
picoCTF{nested_enc0ding_848a466b}
```

