i used this py script,

```python
#!/usr/bin/env python3

from pwn import *
import ast

host = "verbal-sleep.picoctf.net"
port = 56726

# connect to remote service
conn = remote(host, port)

# receive all data (list of encoded values)
hex_value = conn.recvall()

# convert bytes → string → python list
list_hex = ast.literal_eval(hex_value.decode())

flag = ""

# decode logic
for i in list_hex:
    if len(str(i)) == 4:
        flag += chr(int(i / 16))

print(flag)
```

When we connect to the challenge using:

```bash
nc verbal-sleep.picoctf.net 56726
```

we receive a large list of numbers (looks like “shitty hex values”, but actually integers).

* The values are **not actual hex**, but decimal numbers
* Most values are **4 digits long**
* Converting them directly to ASCII doesn’t work

So we try transformations.

For 4-digit values:

```
value / 16 → ASCII
```

Example:

* `1456 / 16 = 91` → `[`
* Continue → forms readable characters

1. Connect to server using pwntools
2. Receive the list of numbers
3. Convert it into a Python list using `ast.literal_eval()`
4. Loop through values
5. Divide by 16
6. Convert to ASCII using `chr()`
7. Build the flag

The decoded output gives the flag:

```
picoCTF{...}
```
