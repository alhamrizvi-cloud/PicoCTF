
After analyzing the source code, I found a **base64-encoded string** along with a key used for Fernet encryption.

At first, I tried to directly decode the base64 string, but it didn’t work. This is because the data is not just base64-encoded, it is actually **Fernet encrypted**.

So instead of decoding, I used the Fernet key from the source code to properly decrypt the payload.

```python
from cryptography.fernet import Fernet
import base64

payload = b'gAAAAABkzWGWvEp8gLI9AcIn5o-ahDUwkTvM6EwF7YYMZlE-_Gf9rcNYjxIgX4b0ltY6bcxKarib2ds6POclRwCwhsRb1LOXVt4Q3ePtMY4BmHFFZlIHLk05CjwigT7hiI9p3sH9e7Cpk1uO90xbHbuy-mfi3nkmn411aBgwxyWpJvykpkuBIG_nty6zbox3UhbB85TOis0TgM0zG4ht0-GUW4wTq2_5-wkw3kV1ZAisLJHzF-Z9oLMmwFZU0UCAcHaBTGDF5BnVLmUeCGTgzVLSNn6BmB61Yg=='

key = base64.b64encode(b'correctstaplecorrectstaplecorrec')

f = Fernet(key)
print(f.decrypt(payload).decode())
```


* The payload starts with `gAAAAA...`, which indicates **Fernet encryption**
* The key is not directly usable, so it must be **base64-encoded first**
* After encoding the key and using Fernet, the payload can be successfully decrypted and we will get the flag tho
