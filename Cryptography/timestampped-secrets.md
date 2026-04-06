
**AES (Advanced Encryption Standard)** is a block cipher, meaning it encrypts data in fixed-size chunks (16 bytes at a time). 

**ECB (Electronic Codebook)** is the simplest mode of operation. It encrypts every block with the exact same key independently. 
* **The Flaw:** If you encrypt the same piece of data twice with the same key, you get the same result. In this challenge, however, the main flaw isn't the mode—it's the **Key**.

---

## 2. The Vulnerability: Predictable Keys
Usually, an encryption key should be a completely random sequence that is impossible to guess. In this script, the key is generated using this logic:

1.  Take the **Unix Timestamp** (the number of seconds since Jan 1, 1970).
2.  Turn that number into a string (e.g., `"1770242606"`).
3.  Hash that string using **SHA-256**.
4.  Use the first **16 bytes** of that hash as the AES key.

### Why this is "Insecure"
Computers are fast. If we know the encryption happened "around" a certain time, we don't have to guess trillions of random keys. We only have to check the few thousand possible timestamps near the hint provided. This is called a **Brute-Force Attack**.

---

## 3. Step-by-Step Breakdown of the Attack

### Step A: Defining the Search Area
The hint gives us a timestamp: `1770242606`. We assume the "real" time used was likely within a few minutes (or seconds) of this number.

### Step B: The Brute-Force Loop
We write a script that acts like a "time machine." It tries every second one by one:
* **Try 1770242600:** Generate key $\rightarrow$ Attempt Decrypt $\rightarrow$ Is it readable? (No)
* **Try 1770242601:** Generate key $\rightarrow$ Attempt Decrypt $\rightarrow$ Is it readable? (No)
* **Try 1770242602:** ...and so on.

### Step C: Recognizing the Flag
How does the script know when it has the right key? We look for the "Magic Header." Every flag in this competition starts with the text `picoCTF{`. If our decryption result contains that string, we've won.

---

## 4. The Solution Script (Simplified)

```python
from hashlib import sha256
from Crypto.Cipher import AES

# 1. Setup the known data
ciphertext = bytes.fromhex("24162f53d9b29255e635230b821cb8baca14461d54b2955401a049477e201fe9")
hint_time = 1770242606

# 2. Start the brute force (checking 1000 seconds before and after the hint)
for t in range(hint_time - 1000, hint_time + 1000):
    
    # 3. Re-create the key exactly how the original script did
    key = sha256(str(t).encode()).digest()[:16]
    
    # 4. Try to decrypt
    cipher = AES.new(key, AES.MODE_ECB)
    decrypted_data = cipher.decrypt(ciphertext)
    
    # 5. Check if we found the flag
    if b"picoCTF" in decrypted_data:
        print(f"Success! Timestamp was: {t}")
        print(f"Flag: {decrypted_data.decode().strip()}")
        break
```

---
output 
```
alham@fedora  ~/Downloads  ▲ python3 test.py
Found Timestamp: 1770242606
Flag: picoCTF{sa3S_sEc9t_081e3371}
 alham@fedora  ~/Downloads  ▲ 
```
## 5. Summary for the Writeup
* **Challenge Type:** Cryptography / Weak Key Derivation.
* **The Problem:** The encryption key was based on `time.time()`, which is a low-entropy (predictable) value.
* **The Fix:** Use a cryptographically secure random number generator (like `secrets` in Python) to generate keys, rather than system time.
* **Lesson Learned:** "Random" is not the same as "Secure." If a human (or an attacker) can guess the input to a key, they can own the data.
