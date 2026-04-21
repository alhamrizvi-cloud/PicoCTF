<img width="1307" height="89" alt="image" src="https://github.com/user-attachments/assets/aacebaf6-25b9-45b5-a2d5-51714eab9b08" />

## Personal Note

This challenge was honestly one of the hardest I’ve done so far in Forensics, It took me around 2 hours to fully solve, mainly because there were a lot of misleading paths and small details that were easy to miss.

I didn’t use tools like FTK Imager or Autopsy. Instead, I relied completely on Sleuth Kit, which made things harder but also more educational.

Working through everything manually helped me understand how disk forensics actually works under the hood, especially concepts like:

* inode-based file extraction
* slack space analysis
* navigating file systems without a GUI

# picoCTF Writeup – Unforgotten Bits

## Initial Analysis

We are given a disk image:

```bash
disk.flag.img
```

Start by listing files using Sleuth Kit:

```bash
fls -o 731136 disk.flag.img
```

We find a `home` directory and inside it a user folder containing notes, images, logs, and encrypted files.

## Exploring Notes

Navigate to the notes directory and extract files:

```bash
fls -o 731136 disk.flag.img 2364
```

Output:

```
1.txt
2.txt
3.txt
```

Extract them:

```bash
icat -o 731136 disk.flag.img 2365 > 1.txt
icat -o 731136 disk.flag.img 2366 > 2.txt
icat -o 731136 disk.flag.img 2408 > 3.txt
```

Contents:

```
1.txt: chizazerite
2.txt: guldulheen
3.txt: I keep forgetting this, but it starts like: yasuoaatrox...
```

Observation:

* The third file hints at a password starting with **yasuoaatrox**

## Clue from IRC Logs and Emails

From IRC logs and deleted emails:

* The user frequently talks about **League of Legends**
* A deleted email references a password strategy inspired by the comic
  xkcd 936

Key idea:

* Password = combination of **4 meaningful words**
* The user adapts it using **League of Legends champions**

## Password Construction

We already have:

```
yasuoaatrox
```

These are champions from
League of Legends

So the password likely continues with **two more champions**

## Wordlist Generation

Get a list of all League of Legends champions (lowercase), then generate combinations:

```python
arr = open('champions.txt').readlines()

with open('wordlist.txt', 'w') as f:
    for i in range(len(arr)):
        for j in range(len(arr)):
            f.write('yasuoaatrox' + arr[i].strip() + arr[j].strip() + '\n')
```

## Cracking Steghide

Use the generated wordlist:

```bash
stegcracker 2406.bmp wordlist.txt
```

Password found:

```
yasuoaatroxashecassiopeia
```

## Extract Hidden Data

```bash
steghide extract -sf 2406.bmp
```

Output:

```
ledger.1.txt.enc
```

This file is encrypted.

## Finding Encryption Keys

The challenge title "Unforgotten Bits" suggests hidden data.

Check **slack space** of files:

```bash
icat -o 731136 disk.flag.img -s 2365 > slack.bin
strings slack.bin
```

We observe unusual numeric patterns.

## Identifying Encoding

From browser history:

* Searches related to number encodings
* One important reference is
  Golden ratio base

The slack data matches **Golden Ratio Base encoding**

## Decoding Slack Data

Using a Python script, decode the data to extract:

* Salt
* Key
* IV

Final values obtained:

```
Salt: 2350e88cbeaf16c9
Key:  a9f86b874bd927057a05408d274ee3a88a83ad972217b81fdc2bb8e8ca8736da
IV:   908458e48fc8db1c5a46f18f0feb119f
```

## Decryption

Use OpenSSL:

```bash
openssl enc -aes-256-cbc -d \
-S 2350e88cbeaf16c9 \
-K a9f86b874bd927057a05408d274ee3a88a83ad972217b81fdc2bb8e8ca8736da \
-iv 908458e48fc8db1c5a46f18f0feb119f \
-in ledger.1.txt.enc \
-out final.txt
```

---

## Final Flag

```bash
cat final.txt
```

Output contains:

```
picoCTF{f473_53413d_81dc60e1}
```

* Always check **slack space** in forensic challenges
* Password hints can come from:

  * notes
  * browsing history
  * user behavior
* Encoding schemes (like Golden Ratio Base) can hide critical data
* Multiple red herrings exist; focus on strong clues

```
picoCTF{f473_53413d_81dc60e1}
```
