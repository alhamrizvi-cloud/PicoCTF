The goal was to decrypt a flag that was XOR-encrypted using a shared secret derived from a Diffie-Hellman key exchange. While Diffie-Hellman is mathematically secure against eavesdroppers who only see public values, this specific instance provided us with the **private exponent** of one of the parties.

### Technical Breakdown

1.  **The Parameters:**
    * $g = 2$: The generator.
    * $p$: A large 1048-bit prime number.
    * $A$: The server's public key ($A = g^a \pmod{p}$).
    * $b$: The client's **private key** (This is the "leaked" information).

2.  **The Vulnerability:**
    In a standard exchange, an attacker only knows $g$, $p$, $A$, and $B$. To find the shared secret, the attacker would need to solve the **Discrete Logarithm Problem** to find $a$ or $b$, which is computationally infeasible for large primes. However, since $b$ was explicitly provided in the file, we can calculate the shared secret exactly as the client would.



3.  **The Math:**
    The shared secret is established by raising the other party's public key to your own private power:
    $$\text{shared} = A^b \pmod{p}$$
    Substituting $A$, we see why this works:
    $$\text{shared} = (g^a)^b \pmod{p} = g^{ab} \pmod{p}$$

4.  **Decryption Logic:**
    The encryption was a simple XOR cipher using the last byte of the shared secret:
    * $\text{key} = \text{shared} \pmod{256}$
    * $\text{flag}[i] = \text{enc}[i] \oplus \text{key}$

---

## Solution Script (Python)
```python
# Extracted values from the challenge
p = 2549189574813286838731164...
A = 9854453750409656602869254...
b = 2531748005435027320362428...
enc_hex = "4d545e527e697b465955624e0e5e4f0e49620404050f5b5b580b40"

# Step 1: Reconstruct the shared secret
shared_secret = pow(A, b, p)

# Step 2: Extract the XOR key (the last byte)
key = shared_secret % 256

# Step 3: XOR the encrypted hex back to plaintext
ciphertext = bytes.fromhex(enc_hex)
flag = "".join(chr(byte ^ key) for byte in ciphertext)

print(f"Flag: {flag}")
```

## Key Takeaway
The security of Diffie-Hellman relies entirely on the secrecy of the exponents $a$ and $b$. If either private exponent is exposed (through a leak, a weak random number generator, or side-channel attacks), the entire encrypted communication is compromised.
