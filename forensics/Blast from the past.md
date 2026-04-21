
## picoCTF – Metadata Timestamp Fix (Fast Writeup)

This challenge was about fixing hidden metadata inside an image so a remote server would accept it.


### Step 1 – Find the hidden timestamp

Using a hex editor (Bless), I found this string inside the image:

```
Image_UTC_Data1700513181420
```

The number at the end is an **epoch timestamp**.

---

### Step 2 – Modify the timestamp

The challenge hint suggested going back to the start of epoch time.

So I replaced the value with:

```
0000000000001
```

Final string:

```
Image_UTC_Data0000000000001
```

Important:

* Replaced **exactly 13 bytes**
* Did not add/remove anything (to avoid corrupting file)

---

### Step 3 – Verify

```
strings original_modified.jpg | grep Image_UTC_Data
```

Output:

```
Image_UTC_Data0000000000001
```

---

### Step 4 – Fix EXIF timestamp

The server also checks metadata time, so I aligned EXIF fields:

```
exiftool \
-DateTimeOriginal="1970:01:01 00:00:00" \
-ModifyDate="1970:01:01 00:00:00" \
-SubSecTimeOriginal=001 \
-SubSecTimeDigitized=001 \
-SubSecTime=001 \
-overwrite_original \
original_modified.jpg
```

This matches:

```
1970:01:01 00:00:00.001
```

---

### Step 5 – Send file

```
nc mimas.picoctf.net <port> < original_modified.jpg
```

### Key Idea

The challenge validates:

* Raw embedded timestamp (hex edit)
* EXIF timestamp consistency

So both needed to match the **epoch start time**.

