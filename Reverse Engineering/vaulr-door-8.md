

The program takes input in the format:

```
picoCTF{<password>}
```

It extracts the inner string and passes it to `checkPassword()`.

The key function is:

```java
public boolean checkPassword(String password) {
    char[] scrambled = scramble(password);
    char[] expected = { ... };
    return Arrays.equals(scrambled, expected);
}
```

So:

* Your input is **scrambled**
* Then compared with a fixed `expected` array

This means the given hex values are the **scrambled result**, not the original password.

Each character is modified using multiple bit swaps:

```java
c = switchBits(c,1,2);
c = switchBits(c,0,3);
c = switchBits(c,5,6);
c = switchBits(c,4,7);
c = switchBits(c,0,1);
c = switchBits(c,3,4);
c = switchBits(c,2,5);
c = switchBits(c,6,7);
```

The helper function:

```java
public char switchBits(char c, int p1, int p2)
```

swaps two bit positions inside a byte.


* Each byte in `expected[]` is a scrambled version of the original character
* Bit swapping is reversible
* To recover the password, we must **apply the swaps in reverse order**


### Reversing the Scramble

To undo the transformation:

* Take each byte from `expected`
* Apply the same `switchBits` operations
* But in **reverse order**

use this script,


```python
def switch_bits(c, p1, p2):
    bit1 = (c >> p1) & 1
    bit2 = (c >> p2) & 1

    if bit1 != bit2:
        c ^= (1 << p1) | (1 << p2)
    return c

def reverse_scramble(c):
    ops = [
        (1,2),
        (0,3),
        (5,6),
        (4,7),
        (0,1),
        (3,4),
        (2,5),
        (6,7)
    ]

    for p1, p2 in reversed(ops):
        c = switch_bits(c, p1, p2)
    return c

expected = [
0xF4, 0xC0, 0x97, 0xF0, 0x77, 0x97, 0xC0, 0xE4,
0xF0, 0x77, 0xA4, 0xD0, 0xC5, 0x77, 0xF4, 0x86,
0xD0, 0xA5, 0x45, 0x96, 0x27, 0xB5, 0x77, 0x94,
0xC1, 0x95, 0xE0, 0xA4, 0xA5, 0x95, 0xF0, 0xE0
]

result = ""

for b in expected:
    result += chr(reverse_scramble(b))

print(result)
```

```
picoCTF{s0m3_m0r3_b1t_sh1fTiNg_89c5bff8}
```
