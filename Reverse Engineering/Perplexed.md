
## Perplexed

We start by dumping the binary using tools like `objdump` and quickly ignore all the usual boilerplate functions like `_init`, `register_tm_clones`, and `__do_global_dtors_aux` since they don’t contribute to the challenge logic.

The real focus is the `check()` function. It first verifies that the input length is exactly 27 characters. If not, it immediately returns failure.

Inside `check()`, three 64-bit constants are loaded using `movabs`. These constants form a hidden byte array (`secret`) which is later compared against user input. The comparison is not done normally — instead, the program performs a **bit-by-bit comparison** between the input and the secret.

The logic extracts individual bits from both the secret and the input using shifts and masks, then compares them using XOR. If any bit differs, the function returns failure. This continues until all relevant bits are checked.

An important observation is that:

* The secret is **23 bytes → 184 bits**
* The input is **27 bytes → 216 bits**
* Only the **first 189 bits (27 × 7)** are actually used

This reveals that the program is effectively encoding the password using **7-bit chunks**, not standard 8-bit bytes.

So instead of guessing the input, we extract the secret from the binary and reverse the bit transformation.

---

## Exploit Script

```python
import struct

# Extracted constants from binary
c1 = 0x617b2375f81ea7e1
c2 = 0xd269df5b5afc9db9
c3 = 0xf467edf4ed1bfed2

# Convert to little-endian bytes
c1_bytes = struct.pack("<Q", c1)
c2_bytes = struct.pack("<Q", c2)
c3_bytes = struct.pack("<Q", c3)

# Reconstruct the secret buffer (based on memory layout)
secret = c1_bytes + c2_bytes[:7] + c3_bytes + b'\x00' * 4

# Convert to bitstream
bits = "".join(f"{byte:08b}" for byte in secret)

# Only first 189 bits are used (27 chars × 7 bits)
bits = bits[:189]

# Recover password from 7-bit chunks
password = ""
for i in range(27):
    chunk = bits[i*7:(i+1)*7]
    password += chr(int(chunk, 2))

print(password)
```

---

## Final Exploit

Run the script to reconstruct the correct input, then pass it to the binary:

```bash
python3 solve.py
./chall
```
