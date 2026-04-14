A Firefox browser extension (.xpi) that hides a flag inside its source code, disguised as an encryption key.

`.xpi` files are just ZIP archives:
```bash
unzip 56102ec0438646c68605-1.0.xpi -d extension/
cat extension/background/main.js
```

Inside `main.js`, two suspicious values stood out:
```javascript
const key = "cGljb0NURnt5b3UncmUgb24gdGhlIHJpZ2h0IHRyYX0="
const webhookUrl = 'gAAAAABmfRjw...'
```

The comment said the key must be a **32-byte url-safe base64 Fernet key**. Decoding the "key" directly:
```python
import base64
base64.b64decode("cGljb0NURnt5b3UncmUgb24gdGhlIHJpZ2h0IHRyYX0=")
# → picoCTF{you're on the right tra}  ← truncated!
```

The real flag was in the Fernet-encrypted `webhookUrl`, decrypted using the key:
```python
from cryptography.fernet import Fernet

key = b"cGljb0NURnt5b3UncmUgb24gdGhlIHJpZ2h0IHRyYX0="
token = b"gAAAAABmfRjwFKUB-X3GBBqaN1tZYcPg5oLJVJ5XQHFogEgcRSxSis1e4qwicAKohmjqaD-QG8DIN5ie3uijCVAe3xiYmoEHlxATWUP3DC97R00Cgkw4f3HZKsP5xHewOqVPH8ap9FbE"

f = Fernet(key)
print(f.decrypt(token).decode())

# → picoCTF{...}
```

- `.xpi` extensions are ZIP files — always unpack and read the JS
- Extensions have access to `webNavigation` — every URL you visit was being silently exfiltrated to a webhook
- Secrets hardcoded in extensions are never safe — even "encrypted" ones expose both the key and ciphertext
  
