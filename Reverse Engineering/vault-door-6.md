

## What is the program doing?

The program takes your input, strips the `picoCTF{...}` wrapper, and checks if your 32-character password is correct using this condition:

```java
if (((passBytes[i] ^ 0x55) - myBytes[i]) != 0)
```

In plain English: **XOR each character of your input with 0x55, and it must equal the stored byte.**


## What is XOR?

XOR (exclusive OR) is a simple math operation on bits. The rule is:

```
0 XOR 0 = 0
0 XOR 1 = 1
1 XOR 0 = 1
1 XOR 1 = 0  ← "exclusive" means both same = 0
```

Think of it like: **"are these two bits different?"** If yes → 1, if no → 0.

### Example with real numbers:

Let's take the letter `n` (the first letter of our answer).

```
'n' in binary  =  0110 1110  (decimal 110)
0x55 in binary =  0101 0101  (decimal 85)

XOR them bit by bit:
  0110 1110
  0101 0101
  ---------
  0011 1011  =  0x3b  (decimal 59)
```

And look — `0x3b` is exactly the **first value** in `myBytes`! ✅


## The Magic Property of XOR

XOR has a superpower:

> **If A XOR B = C, then C XOR B = A**

It is perfectly reversible. Example:

```
5 XOR 3 = 6
6 XOR 3 = 5   ← you get back the original!
```

So if the program checks:

```
input[i] XOR 0x55 = myBytes[i]
```

Then to find the original input, we just flip it:

```
input[i] = myBytes[i] XOR 0x55
```

## Solving It

We just XOR every stored byte with `0x55`:

```python
myBytes = [0x3b, 0x65, 0x21, 0xa, 0x38, 0x0, 0x36, 0x1d, ...]

password = ''.join(chr(b ^ 0x55) for b in myBytes)
```

Let's trace the first 4 bytes by hand:

| `myBytes[i]` | XOR `0x55` | Result | Character |
|---|---|---|---|
| `0x3b` = 59 | 59 XOR 85 | 110 | `n` |
| `0x65` = 101 | 101 XOR 85 | 48 | `0` |
| `0x21` = 33 | 33 XOR 85 | 116 | `t` |
| `0x0a` = 10 | 10 XOR 85 | 95 | `_` |

That gives us `n0t_` — the start of `n0t_mUcH_h4rD3r_tH4n_x0r`!

## Final Answer

```
picoCTF{n0t_mUcH_h4rD3r_tH4n_x0r_d8aeba4}
```

The name says it all, "not much harder than XOR." The minion read only the first few pages of the cryptography book, used a single XOR key (`0x55`) for every byte, and forgot that XOR is trivially reversible. Real encryption (like AES) uses XOR too, but combined with many other operations to make reversal practically impossible.
