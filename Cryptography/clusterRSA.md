another RSA , here we got values of n, or ct smthg,

n value is so much large, i used factorDB,

https://factordb.com/index.php?query=8749002899132047699790752490331099938058737706735201354674975134719667510377522805717156720453193651
<img width="1361" height="366" alt="image" src="https://github.com/user-attachments/assets/a3f0200f-6217-4efd-b308-b28d632c5fde" />

n = p × q × r × s   (4 primes!)

Instead of:

φ(n) = (p-1)(q-1)

Now:

φ(n) = (p-1)(q-1)(r-1)(s-1)

Everything else stays same:

d = e⁻¹ mod φ(n)
m = ct^d mod n

so i made this python script,

## Final Python (this will give flag)

Run this:

```python
from Crypto.Util.number import inverse, long_to_bytes

n = 8749002899132047699790752490331099938058737706735201354674975134719667510377522805717156720453193651
e = 65537
ct = 3891662771105467488888140657249806558204248580982414398721303729411975827561400201060615350757604497

p = 9671406556917033397931773
q = 9671406556917033398314601
r = 9671406556917033398439721
s = 9671406556917033398454847

phi = (p-1)*(q-1)*(r-1)*(s-1)

d = inverse(e, phi)

m = pow(ct, d, n)

print(long_to_bytes(m))
```

## 🧠 Why this worked

* Multi-prime RSA is weaker in CTFs because:

  * easier to factor (smaller primes)
  * FactorDB already had it

