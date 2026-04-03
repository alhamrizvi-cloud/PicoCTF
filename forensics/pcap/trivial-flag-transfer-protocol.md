
We are given a file and need to find the hidden flag.

First, I downloaded the file:

```
wget https://mercury.picoctf.net/static/fb24ddcfaf1e297be525ba7474964fa5/tftp.pcapng
```

Then I checked what type of file it is:

```
file tftp.pcapng
```

It turned out to be a **network cdnfure file**.

---

### 🔍 Opening the File

I used **Wireshark** to open it:

```
wireshark tftp.pcapng
```

There was a lot of traffic, but most of it was **TFTP**.

---

### 📘 What is TFTP?

TFTP is a simple file transfer protocol:

* Uses **UDP**
* No encryption
* No authentication

So basically, data is sent openly — not secure.

---

### 📂 Finding Files

I used a filter in Wireshark:

```
tftp.type
```

Then I saw these files:

* instructions.txt
* plan
* picture1.bmp
* picture2.bmp
* picture3.bmp

I exported all files:
**File → Export Objects → TFTP → Save All**

---

### 📄 First File: instructions.txt

```
cat instructions.txt
```

Output looked like garbage:

```
GSGCQBRFAGRAPELCGBHEGENSSVPFBJRZHFGQVFTHVFRBHESYNTGENAFSRE...
```

This looked encoded.

I tried **ROT13** (common in CTFs).

After decoding:

```
TFTP DOESNT ENCRYPT OUR TRAFFIC SO WE MUST DISGUISE OUR FLAG TRANSFER...
```

👉 Meaning:
We need to hide the flag somehow.

---

### 📄 Second File: plan

```
cat plan
```

Again encoded text:

```
VHFRQGURCEBTENZNAQUVQVGJVGU-QHRQVYVTRAPR...
```

After ROT13:

```
I USED THE PROGRAM AND HID IT WITH-DUE DILIGENCE. CHECK OUT THE PHOTOS
```

👉 Important clue:

* Use a program
* Password might be **DUEDILIGENCE**
* Check images

---

### 💻 Program File

We also got:

```
program.deb
```

Installed it:

```
sudo dnf install ./program.deb
```

It actually installs a tool called:
👉 **steghide**

---

### 🖼️ What is Steghide?

It hides secret data inside images.

Command:

```
steghide extract -sf <filename> -p <password>
```

---

### 🔓 Extracting the Flag

Tried password **DUEDILIGENCE** on images:

```
steghide extract -sf picture1.bmp -p DUEDILIGENCE
❌ No data

steghide extract -sf picture2.bmp -p DUEDILIGENCE
❌ No data

steghide extract -sf picture3.bmp -p DUEDILIGENCE
✅ Success!
```

It created a file:

```
flag.txt
```

---

### 🏁 Final Flag

```
cat flag.txt
```

Output:

```
picoCTF{h1dd3n_1n_pLa1n_51GHT_18375919}
```

---

### ✅ Conclusion

* Used Wireshark to analyze traffic
* Found hidden files
* Decoded ROT13 messages
* Used steghide to extract hidden data
* Got the flag 🎉

