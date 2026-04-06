The challenge involved an RSA-encrypted message where the public key was missing, but the private key was hinted to be "carelessly handled." The goal was to locate the leaked key, reconstruct it, and decrypt the flag.

### Technical Breakdown

1.  **Metadata Discovery:**
    By running `exiftool` on the provided image, a long hex-encoded string was discovered in the **Comment** field.
    
    

2.  **Hex to Plaintext (CyberChef):**
    The string started with `2d2d2d2d2d4245...`. Inputting this into CyberChef using the **From Hex** recipe revealed the following header:
    `-----BEGIN PRIVATE KEY-----`
    This confirmed that the entire RSA Private Key was embedded directly into the image's metadata in hex format.

3.  **The RSA Mechanism:**
    RSA is an asymmetric encryption algorithm. Under normal circumstances:
    * **Encryption:** Plaintext is raised to the power of the public exponent $e$ modulo $n$.
    * **Decryption:** Ciphertext is raised to the power of the private exponent $d$ modulo $n$.
    
    Since the private key ($d$) was leaked, we didn't need to perform any advanced factoring on $n$ (like a Wiener's attack or using factorDB); we simply used the key as intended.

    

---

## Steps Taken to Solve

### 1. Extraction and Decoding
The hex string was copied from the terminal and decoded via **CyberChef**. The resulting Base64-style text was saved as `private.pem`.

### 2. File Preparation
The private key was saved to the local directory:
```bash
nano private.pem  # Pasted the decoded "BEGIN PRIVATE KEY" block here
```

### 3. Decryption
Using the `openssl` utility, the encrypted flag was processed using the recovered private key:
```bash
openssl pkeyutl -decrypt -inkey private.pem -in flag.enc -out flag.txt
```

**The Result:**
```text
picoCTF{rs4_k3y_1n_1mg_4eedd678}
```

## Key Takeaway
This challenge highlights **Information Leakage**. Even the strongest cryptographic algorithms (like RSA) are rendered useless if the private keys are stored in insecure locations, such as image metadata, public git repositories, or configuration files. Always "sanitize" your files before sharing them!
