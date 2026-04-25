
# MiniRSA - PicoCTF Walkthrough | Cryptography | By Alham Rizvi

Hello folks, this is your chad Alham Rizvi again with another writeup.

Today we've got a Mini RSA challenge, and it's pretty interesting because I already worked on RSA yesterday, covering most of the core concepts. So this one feels familiar but still tricky in its own way.

In this challenge, we are given a very small public exponent value, **e = 3**. Normally in RSA, `e` is larger (commonly 65537), but here it is intentionally small, which hints toward a **low public exponent attack**.

---

## Given values

**N:**
29331922499794985782735976045591164936683059380558950386560160105740343201513369939006307531165922708949619162698623675349030430859547825708994708321803705309459438099340427770580064400911431856656901982789948285309956111848686906152664473350940486507451771223435835260168971210087470894448460745593956840586530527915802541450092946574694809584880896601317519794442862977471129319781313161842056501715040555964011899589002863730868679527184420789010551475067862907739054966183120621407246398518098981106431219207697870293412176440482900183550467375190239898455201170831410460483829448603477361305838743852756938687673

**e:** 3

**ciphertext (c):**
2205316413931134031074603746928247799030155221252519872649677524109884270887923602732938126567362476479451332015075169723712525604198019722680472298833460936186285820654176881133843867419473198122162196277755395450281493393168969719023717

---

## Key Idea

Since **e = 3**, if the plaintext `m` is small enough such that:

[
m^3 < N
]

then encryption becomes:

[
c = m^3
]

So decryption is simply:

[
m = \sqrt[3]{c}
]

This means we can directly recover the plaintext using an **integer cube root attack**, instead of performing factorization or modular inverse operations.

---

## Exploit Code

```python
import gmpy2

# Ciphertext (split into readable format)
c = 22053164139311340310746037469282477990301552212525198726496\
7752410988427088792360273293812656736247647945133201507516972371252\
5604198019722680472298833460936186285820654176881133843867419473198\
122162196277755395450281493393168969719023717

# Step 1: Compute integer cube root
m = gmpy2.iroot(c, 3)[0]

# Step 2: Convert integer → hex → bytes
flag = bytes.fromhex(hex(m)[2:])

# Step 3: Print result
print(flag)
```

---

## Explanation of Exploit Steps

### 1. Import gmpy2

We import `gmpy2` because it supports efficient big integer math and provides the `iroot()` function for computing integer roots.

---

### 2. Store ciphertext

We assign the ciphertext `c`.
The backslashes (`\`) are only used to split the long number across multiple lines for readability.

---

### 3. Compute cube root

```python
m = gmpy2.iroot(c, 3)[0]
```

* `iroot(c, 3)` computes the integer cube root of `c`
* It returns:

  * root value
  * boolean (whether it is exact)

We only take `[0]` because we only need the root.

This works because:
[
c = m^3
]

---

### 4. Convert integer to readable format

```python
bytes.fromhex(hex(m)[2:])
```

Breakdown:

* `hex(m)` → converts integer to hexadecimal
* `[2:]` → removes `0x` prefix
* `bytes.fromhex()` → converts hex to raw bytes

This converts the number back into the original plaintext message.

---

## Output

```
b'picoCTF{n33d_a_lArg3r_e_955da27a}'
```

That is the recovered flag.

---

## Summary of Attack Flow

* Import big integer math tool (`gmpy2`)
* Load ciphertext
* Compute integer cube root (because `e = 3`)
* Convert result → hex → bytes
* Recover flag

---

## Final Insight

This attack works because:

* No padding (like OAEP) was used
* Public exponent is very small
* Message was small enough such that ( m^3 < N )

This is why low-exponent RSA is dangerous when implemented incorrectly.
