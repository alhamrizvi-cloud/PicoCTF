### Printer Shares — Writeup

**Challenge:** Printer Shares
**Category:** General Skills

---

#### Description

We are given a network printer service running on port `57520`. The hint suggests using `smbclient`, indicating that the service is likely an SMB file share.

---

#### Step 1: Check Connectivity

First, verify that the service is reachable:

```bash
nc -vz mysterious-sea.picoctf.net 57520
```

The connection succeeds, confirming the service is active.

---

#### Step 2: Enumerate SMB Shares

List available SMB shares using:

```bash
smbclient -L //mysterious-sea.picoctf.net -p 57520
```

After pressing Enter for the password (anonymous login), we see:

```
shares          Disk      Public Share With Guests
IPC$            IPC       IPC Service
```

---

#### Step 3: Connect to the Share

Connect to the accessible share:

```bash
smbclient //mysterious-sea.picoctf.net/shares -p 57520
```

Again, press Enter for the password.

---

#### Step 4: List Files

Inside the SMB shell:

```bash
ls
```

Output:

```
dummy.txt
flag.txt
```

---

#### Step 5: Retrieve the Flag

Download the flag file:

```bash
get flag.txt
```

Exit the SMB session and read the file:

```bash
cat flag.txt
```

---

#### Flag

```
picoCTF{5mb_pr1nter_5h4re5_2f61915b}
```
