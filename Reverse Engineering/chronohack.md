

We are given the following source code:

```python
import random
import time

def get_random(length):
    alphabet = "0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz"
    random.seed(int(time.time() * 1000))  # seeding with current time 
    s = ""
    for i in range(length):
        s += random.choice(alphabet)
    return s
```

### Vulnerability

The token is generated using:

```python
random.seed(int(time.time() * 1000))
```

This means the PRNG depends entirely on the current timestamp (in milliseconds), making it predictable.


### Approach

* Recreated the token generator locally
* Connected to the remote service using pwntools
* Took local time before sending guesses
* Brute-forced nearby timestamps (to handle latency)
* Tried 50 guesses per connection (server limit)
* Reconnected and repeated with adjusted offsets


### Solution Script

```python
from pwn import *
import random
import time

def get_random(length, seed):
    alphabet = "0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz"
    random.seed(seed)
    return ''.join(random.choice(alphabet) for _ in range(length))

HOST = "verbal-sleep.picoctf.net"
PORT = 64493

found_flag = False

for adjust in range(-50, 1000, 40):
    print(f"[+] Adjustment: {adjust}")

    start_time = time.time()
    r = remote(HOST, PORT)

    for i in range(50):
        seed = int(start_time * 1000) + i + adjust
        token = get_random(20, seed)

        try:
            r.recvuntil(b"(or exit):")
        except:
            break

        print(f"Trying: {token}")
        r.sendline(token.encode())

        res = r.recvline()

        if b"Sorry" not in res:
            print("\n" + "="*100)
            print("[+] Token Found:", token)
            print(res.decode())
            print(r.recvline().decode())
            found_flag = True
            break

    r.close()

    if found_flag:
        break
```

### Result

The correct token was found by matching the seed, and the flag was returned:

```
picoCTF{...}
```

