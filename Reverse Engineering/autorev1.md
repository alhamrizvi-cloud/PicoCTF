## Challenge Overview

We connect to the remote service using:

```bash
nc mysterious-sea.picoctf.net 52432
```

The server responds:

* Sends **20 ELF binaries**
* Each must be solved in **~1 second**
* We must extract a **secret integer** from each binary
* After 20 correct answers → we get the flag

---

## Initial Observation

When connecting, sometimes a number appears before the binary:

```
2954743770
Here's the next binary in bytes:
```

At first glance, this looks like the answer — and for the **first round**, it actually is.

So initially:

* We can **copy-paste the number**
* Submit it
* Get `"Correct!"`

But this only works temporarily.

---

## Core Problem

* There are **20 rounds**
* Each binary is different
* Each has a **1-second time limit**
* Manual reversing is **impossible at scale**

Even if you solve 2–3 manually, you will fail overall due to timeout.

---

## Binary Analysis

Looking at the ELF binary:

* It uses:

  ```c
  scanf("%u", &input);
  ```
* Then compares input with a hardcoded value:

  ```c
  if (input == secret)
      puts("Correct!");
  else
      puts("Nice try :(");
  ```

---

## Key Insight

From disassembly pattern:

```
c7 45 fc XX XX XX XX
```

This corresponds to:

```asm
mov DWORD PTR [rbp-0x4], <secret>
```

So:

* `c7 45 fc` → instruction opcode
* Next **4 bytes** → the secret (little-endian)

---

## Extraction Strategy

Instead of reversing fully:

1. Convert hex → raw bytes
2. Search for:

   ```python
   b'\xc7\x45\xfc'
   ```
3. Read next 4 bytes as:

   ```python
   struct.unpack('<I', ...)
   ```
4. That value = **secret**

---

## Automation (Pwntools Script)

Because of strict timing, automation is required.

### Key Logic

* Connect to remote
* Loop 20 times:

  * Receive hex ELF
  * Convert to bytes
  * Extract secret using opcode pattern
  * Send answer immediately

---

## Full Script

```python
#!/usr/bin/env python3
from pwn import *
import struct

def extract_secret(binary_data: bytes) -> int:
    pattern = b'\xc7\x45\xfc'
    idx = binary_data.find(pattern)
    
    if idx != -1:
        return struct.unpack_from('<I', binary_data, idx + 3)[0]
    
    return None


def solve():
    conn = remote('mysterious-sea.picoctf.net', 52432)

    conn.recvuntil(b'Good luck >:)\n')

    for i in range(20):
        conn.recvuntil(b"Here's the next binary in bytes:\n")
        hex_line = conn.recvline().strip()

        binary_data = bytes.fromhex(hex_line.decode())
        secret = extract_secret(binary_data)

        conn.recvuntil(b"What's the secret?:")
        conn.sendline(str(secret).encode())

        print(conn.recvline().decode())

    print(conn.recvall().decode())
    conn.close()


if __name__ == '__main__':
    solve()
```

---

## Execution Output

* Script solves all rounds instantly
* No timeout issues
* Fully automated reversing

Final output:

```
Woah, how'd you do that??
Here's your flag: picoCTF{4u7o_r3v_g0_brrr_78c345aa}
```

---

## Final Flag

```
picoCTF{4u7o_r3v_g0_brrr_78c345aa}
```

---

## Key Takeaways

* Pattern recognition in assembly is extremely powerful
* Full reversing is not always required
* Automation is critical in time-constrained challenges
* Understanding compiler patterns (like `mov [rbp-4], imm32`) gives huge advantage
