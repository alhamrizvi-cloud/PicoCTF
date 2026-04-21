
# Bitlocker-1 Writeup


We are given a file:

```
bitlocker-1.dd
```

```bash
bitlocker2john -i bitlocker-1.dd > hash.txt
```

This extracts hashes from the disk image.

Inside `hash.txt`, we focus on the **User Password hash**, because:

* Recovery keys are too long to brute-force
* User password is shorter and crackable

## Step 3 — Prepare Wordlist

Instead of using the full huge wordlist, we take a smaller chunk:

```bash
head -n 10000 /usr/share/wordlists/rockyou.txt > wordlist.txt
```

This saves time while still being effective.

```bash
hashcat -m 22100 -a 0 hash.txt wordlist.txt -w 3
```

Explanation:

* `-m 22100` → BitLocker hash mode
* `-a 0` → dictionary attack
* `-w 3` → faster processing

After a short time, we get:

```
Password: jacqueline
```
## Step 5 — Decrypt BitLocker Image

Now we use `dislocker` to unlock the disk.

First, create folders:

```bash
mkdir dislocker mount
```

Then run:

```bash
sudo dislocker -V bitlocker-1.dd -u"jacqueline" -- dislocker
```

This creates a file:

```
dislocker/dislocker-file
```

```bash
sudo mount -o loop dislocker/dislocker-file mount
```

Now go inside:

```bash
cd mount
ls
``

Search for flag:

```bash
grep -R "picoCTF" .
```

Or directly open files.

You will find:

```
picoCTF{us3_b3tt3r_p4ssw0rd5_pl5!_3242adb1}
```

## Step 8 — Cleanup (Optional)

```bash
sudo umount mount
sudo umount dislocker
```


```
picoCTF{us3_b3tt3r_p4ssw0rd5_pl5!_3242adb1}
```
