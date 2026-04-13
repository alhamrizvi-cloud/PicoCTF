

The program builds a tiny 5-block chain (1 genesis + 4 mined blocks). Each block's SHA-256 hash must start with `"00"` (proof-of-work). The final plaintext that gets encrypted is all five hashes joined by `-`:

```
<hash0>-<hash1>-<hash2>-<hash3>-<hash4>
```

The **flag (token)** is then **spliced into the exact middle** of that string before encryption.


```python
key_hash = hashlib.sha256(key).digest()   # computed ONCE — 32 bytes

for i in range(0, len(plaintext), 16):
    block = plaintext[i:i+16]
    cipher_block = xor_bytes(block, key_hash)   # same key_hash every block!
    ciphertext += cipher_block
```

**The fatal mistake:** `key_hash` is computed once and XORed with *every* 16-byte block. This is identical to a **repeating-key XOR cipher** with a 32-byte key — but since each block is only 16 bytes, only the first 16 bytes of `key_hash` are ever used, making it even weaker.

There is **no IV, no chaining, no mode of operation** — it's just:

```
ciphertext_block = plaintext_block XOR key_hash[:16]
```

Since XOR is its own inverse, decryption is **identical** to encryption:

```
plaintext_block = ciphertext_block XOR key_hash[:16]
```

You don't need to brute-force anything. You already have the key, so you just SHA-256 it and XOR each block — done.

The key is 32 bytes (AES-256 size) and the block size is 16 bytes (AES block size), so it *looked* like AES-CBC at first glance. That's why none of the AES decryption attempts worked — it was never AES at all. Just homebrew XOR dressed up to look like it

Thanks claude for this py script


```
import hashlib

# ── Inputs ────────────────────────────────────────────────────────────────────
key = b'Z\xa3\xf6\xd4\xdb\x8a\x9c\x10\x84\xf8\xb6\xb0:\x1c\xce\xca\xbfX\x96\x9d\x87\tm\xd6\xbe4a\xc5\xd5\x91^\x98'

encrypted = (
    b'o\xd3\xefR\xf9@\x01(\xebg\xf0\xed\xc5K\xcc\x87>\x83\xe8\x05\xaeB\x02)\xbe2\xa7\xe2\xc6\x19\x97\xd2'
    b'h\xd9\xeaW\xfcF\x05+\xb20\xf4\xb7\x97N\x9f\xd5e\xd2\xeb\x02\xf9FVx\xeb`\xa2\xe4\xc7M\x9c\x85p'
    b'\xd1\xe8P\xffOR.\xee5\xa6\xb6\x94\x18\x9e\x84i\xd6\xbaQ\xf9\x14\x00.\xee3\xa2\xe4\x93\x1c\x9a\x83h'
    b'\x87\xea\x01\xf8FV"\xbbe\xa2\xb6\x93N\x9b\xd5l\xd4\xbaU\xa8\x17Z.\xbdb\xa6\xe5\xc7H\xcc\x8fk\xcc'
    b'\xe8P\xa9C\x07(\xbd<\xf4\xe7\xc6\x18\x99\xd0;\x82\xe0\x01\xa8GQz\xeb6\xaf\xe5\xc7H\xca\x829\xd2\xa8'
    b'\t\xa9\x19 O\xcc\x7f\xf4\xb9\x99\x1e\xc5\xe9n\xb2\x8a\x08\x9c\x1f1y\xde5\xe7\xb6\xae"\xf6\xe37\xac'
    b'\xe8\x12\xfeO\x00S\xd5u\xd5\xaf\x9b7\xf4\xcc\x1f\xaa\x87T\xff\x15\x07)\xeb1\xa4\xa8\x92D\x9b\xd5k'
    b'\x87\xebQ\xac\x17U,\xb93\xf2\xe4\x92I\x96\xd3l\x87\xbcQ\xa8DV*\xb3f\xf4\xb6\xdbM\x9e\x82e\xd6\xbc'
    b'Y\xfdNQ"\xbc4\xf3\xe4\xc4I\x97\x858\xd8\xbcQ\xabDZ+\xe83\xa7\xb7\xc2I\x9f\xd7d\x80\xee\x01\xafG'
    b'\x07,\xbf7\xa3\xe6\x95\x1c\x99\xd7m\xd0\xb9Y\xacCU}\xece\xaf\xb3\x90P\x9e\x86n\x82\xbaQ\xae\x14W'
    b'/\xec`\xf0\xe7\xc0N\x9a\x81m\xd6\xe8Y\xa8BU(\xbd6\xa1\xe1\xc1J\x97\x81j\x87\xbbV\xfeBW~\xbf3\xa6'
    b'\xb6\xc2\x18\x97\xd0>\xd4\xeaU\xf8\x13\x01-\xeb2\xf2\xe5\xc5\x19\xac\xb4'
)

# ── Step 1: derive the same key_hash the encryptor used ───────────────────────
key_hash = hashlib.sha256(key).digest()          # 32 bytes
print(f"[*] SHA-256(key) = {key_hash.hex()}\n")

# ── Step 2: XOR every 16-byte block with key_hash (same operation as encrypt) ─
BLOCK = 16
plaintext = b''
for i in range(0, len(encrypted), BLOCK):
    chunk = encrypted[i:i + BLOCK]
    dec   = bytes(c ^ k for c, k in zip(chunk, key_hash))   # key_hash reused!
    plaintext += dec

decoded = plaintext.decode('utf-8', errors='replace')
print(f"[*] Full decrypted plaintext:\n{decoded}\n")

# ── Step 3: extract the flag ──────────────────────────────────────────────────
start = decoded.find('picoCTF{')
end   = decoded.find('}', start) + 1
flag  = decoded[start:end]
print(f"[+] FLAG: {flag}")

```
