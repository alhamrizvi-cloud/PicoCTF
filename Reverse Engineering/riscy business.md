# Riscy Business writeup


You get a binary called `riscy`.

It asks for a flag.

If correct → **"Success!"**
If wrong → sad message.

## 🔍 Step 1: Basic Recon

```bash
file riscy
```

Output:

```
ELF 64-bit, RISC-V, statically linked, stripped
```

💡 Meaning:

* Not normal x86 → it's **RISC-V**
* Need emulator → use `qemu-riscv64`
* No symbols → harder reversing
## ▶️ Step 2: Run It

```bash
./riscy
```

Behavior:

* < 8 chars → "too short"
* ≥ 8 chars → "too riscy"

💡 So input must be **at least 8 bytes**

## 🧪 Step 3: What is it doing?

Open in Ghidra → look at main logic

Flow is basically:

```
1. Take input
2. First 8 bytes → used as KEY
3. Run RC4 encryption on input
4. Compare result with stored bytes
5. If match → Success
```

## ⚠️ IMPORTANT LOGIC

👉 This is the core:

```
KEY = input[0:8]
cipher = RC4(input, KEY)

if cipher == target:
    Success
```

💀 Problem:

* Key depends on input
* Input depends on key

➡️ circular dependency → can't solve normally

## 🔐 Step 4: Understanding Trick (THE REAL IDEA)

We don’t reverse RC4 fully.

We **abuse the binary itself**.

### Trick:

Let the binary encrypt for us
Then just **compare partial results*

## Key Observation

At comparison point:

* `s1` → our encrypted input
* `a5` → correct encrypted target

So we can do:

```
compare(s1, a5)
```
## 💡 Plan

We brute force **one character at a time**

```
known = "picoCTF{"
```

Then:

```
try picoCTF{a
try picoCTF{b
try picoCTF{c
...
```

Each time:

* Let binary encrypt it
* Compare first N bytes

If match → correct char

## Why this works

RC4 is **deterministic**

If prefix is correct:
→ encrypted prefix also matches

So we grow flag slowly:

```
picoCTF{4
picoCTF{4n
picoCTF{4ny
...
```

##  Exploit 

```python
#!/usr/bin/env python3
from pwn import *
import string

chars = string.ascii_letters + string.digits + string.punctuation

flag = b"picoCTF{"

qemu = process(["qemu-riscv64", "-g", "9000", "./riscy"])
gdb = process(["gdb-multiarch", "-q"])

gdb.sendlineafter(b"(gdb)", b"target remote localhost:9000")
gdb.sendlineafter(b"(gdb)", b"b *0x101c0")

def send_guess(g):
    gdb.sendline(b"c")
    qemu.recvuntil(b"> ")
    qemu.sendline(g)
    gdb.recvuntil(b"Breakpoint")

def dump(reg):
    gdb.sendline(b"x/60xb $" + reg)
    out = gdb.recvuntil(b"(gdb)")
    data = []
    for line in out.split(b"\n"):
        if b":" in line:
            parts = line.split(b":")[1].strip().split()
            data += parts
    return bytes([int(x,16) for x in data])

# get target encrypted
send_guess(flag)
target = dump(b"a5")

for i in range(len(flag), 52):
    for c in chars:
        guess = flag + c.encode()

        gdb.sendline(b"set $pc = 0x10112")
        gdb.recvuntil(b"(gdb)")

        send_guess(guess)
        enc = dump(b"s1")

        if enc[:i+1] == target[:i+1]:
            flag = guess
            print(flag)
            break

print("FLAG:", flag.decode())
```

---

## Final Flag

```
picoCTF{4ny0n3_g0t_r1scv_h4rdw4r3?_LGUfwl8xyMUlpgvz}
```
