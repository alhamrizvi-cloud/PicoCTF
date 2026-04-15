We are given a Java program that checks a password inside the format:

```
picoCTF{<32_characters>}
```

The goal is to find the correct 32-character string that passes the `checkPassword()` function.

The important part of the code is:

```java
public boolean checkPassword(String password) {
    if (password.length() != 32) {
        return false;
    }
    int[] x = passwordToIntArray(password);
    return x[0] == 1096770097
        && x[1] == 1952395366
        && x[2] == 1600270708
        && x[3] == 1601398833
        && x[4] == 1716808014
        && x[5] == 1734293606
        && x[6] == 909455713
        && x[7] == 1664103218;
}
```

So:

* The password is converted into an array of 8 integers
* Each integer represents **4 characters**
* Total: `8 × 4 = 32 characters`

The function `passwordToIntArray()` packs 4 characters into 1 integer:

```java
x[i] = hexBytes[i*4]   << 24
     | hexBytes[i*4+1] << 16
     | hexBytes[i*4+2] << 8
     | hexBytes[i*4+3];
```

This means:

* Each character = 1 byte (ASCII)
* 4 bytes = 32-bit integer

So the integer is just:

```
[char1][char2][char3][char4]
```

To recover the password, we reverse the process:

* Extract 4 bytes from each integer
* Convert each byte back to a character

#### Extraction formula:

```python
char1 = (num >> 24) & 0xff
char2 = (num >> 16) & 0xff
char3 = (num >> 8)  & 0xff
char4 = num & 0xff
```

---

### Exploit Script

```python
nums = [
    1096770097,
    1952395366,
    1600270708,
    1601398833,
    1716808014,
    1734293606,
    909455713,
    1664103218
]

result = ""

for n in nums:
    result += chr((n >> 24) & 0xff)
    result += chr((n >> 16) & 0xff)
    result += chr((n >> 8) & 0xff)
    result += chr(n & 0xff)

print(result)
```

---

### Output

```
A_b1t_0f_b1t_sh1fTiNg_e9e0a8a9
```

---

### Final Flag

```
picoCTF{A_b1t_0f_b1t_sh1fTiNg_e9e0a8a9}
```
