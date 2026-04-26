
# Mini RSA – PicoCTF Writeup | By Alham Rizvi

## The Problem

We're given a RSA-encrypted message with an unusually small public exponent:

- `e = 3`
- A large `n` and ciphertext `c`

## Why This is Vulnerable

In RSA, encryption is: `c = m^e mod n`

When `e = 3` and the plaintext `m` is small relative to `n`, something bad happens — `m^3` might be **smaller than `n` itself**, which means the modular reduction never actually kicks in. So:

```
c = m^3 mod n = m^3
```

The modulo does nothing. That means we can just take the **plain integer cube root** of `c` to recover `m` directly — no private key needed.

## What if m^3 is slightly larger than n?

If `m` is a bit larger, then `c = m^3 - k*n` for some small integer `k`. So we try:

```
m = cuberoot(c + k*n)   for k = 0, 1, 2, ...
```

We stop when the cube root is exact (no remainder).

## The Script

```python
import gmpy2

n = 1615765684321463054078226051959887884233678317734892901740763321135213636796075462401950274602405095138589898087428337758445013281
e = 3
c = 1220012318588871886132524757898884422174534558055593713309088304910273991073554732659977133980685370899257850121970812405700793710

k = 0
while True:
    M = k*n + c
    m, exact = gmpy2.iroot(M, e)   # integer cube root
    if exact:
        message_hex = hex(m)[2:]
        message_decoded = bytearray.fromhex(message_hex).decode()
        print("FLAG:", message_decoded)
        break
    k += 1
```

## Result

```
picoCTF{e_sh0u1d_b3_lArg3r_7adb35b1}
```
