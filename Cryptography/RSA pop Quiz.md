
# PicoCTF – RSA Pop Quiz Writeup

## Challenge Overview

**Name:** RSA Pop Quiz
**Category:** Cryptography
**Description:** A netcat-based challenge where "Ms. Adleman-Shamir-Rivest" gives you a series of RSA problems. For each one, you must decide if it's possible/feasible, and if so, compute the answer.

---

## Background: RSA Basics

RSA relies on these core relationships:

- `n = p × q` (public modulus, product of two large primes)
- `totient(n) = (p-1)(q-1)`
- `e` = public exponent
- `d = e⁻¹ mod totient(n)` (private key)
- **Encrypt:** `ciphertext = plaintext^e mod n`
- **Decrypt:** `plaintext = ciphertext^d mod n`

---

## Problem-by-Problem Breakdown

### Problem 1 — Find `n` given `p` and `q`
**Answer: Y**
```
n = p × q = 60413 × 76753 = 4636878989
```
Trivial multiplication.

---

### Problem 2 — Find `q` given `p` and `n`
**Answer: Y**
```
q = n / p = 5051846941 / 54269 = 93089
```
Since `n = p × q`, just divide.

---

### Problem 3 — Find `p` and `q` given only `e` and `n`
**Answer: N**

This would require **factoring `n`**, which is a 617-digit number. RSA security is based entirely on the fact that this is computationally infeasible for large primes. No known algorithm can do this in reasonable time.

---

### Problem 4 — Find `totient(n)` given `p` and `q`
**Answer: Y**
```
totient(n) = (p-1)(q-1) = (12611-1)(66347-1) = 836623060
```

---

### Problem 5 — Find `ciphertext` given `plaintext`, `e`, `n`
**Answer: Y**
```python
ciphertext = pow(plaintext, e, n)
```
Standard RSA encryption.

---

### Problem 6 — Find `plaintext` given `ciphertext`, `e`, `n` (no `p`, `q`, or `d`)
**Answer: N**

Without the private key `d` or the prime factors `p` and `q`, decryption is not feasible. This is equivalent to Problem 3 — you'd need to factor `n` first.

---

### Problem 7 — Find `d` given `p`, `q`, `e`
**Answer: Y**
```python
totient = (p-1) * (q-1)
d = pow(e, -1, totient)  # modular inverse
```
With both primes known, computing `d` is straightforward.

---

### Problem 8 — Find `plaintext` given `p`, `ciphertext`, `e`, `n`
**Answer: Y**

This is the key problem. Even though we don't have `q` directly, we have `p` and `n`, so:

```python
p = 153143042272527868798412612417204434156935146874282990942386694020462861918068684561281763577034706600608387699148071015194725533394126069826857182428660427818277378724977554365910231524827258160904493774748749088477328204812171935987088715261127321911849092207070653272176072509933245978935455542420691737433
ciphertext = 20765750695406866859450907483940406487988113182607262133235375739231782816761136009981213827836531804797112736811836568211405398901062590045326029958064825130640979190368888828402038167100458028144662958398060720450782616181024579250156748485766523568609058109278712259243825645218158041332811497584818191443651827418437497089320612903115204136524645824442949497490841805354285293586459203767623631716852243598507432993874328070727318071041589507583627745427173540063737159312755977021109780824124172904998458632404777612521408124774048446641394820413039325568679760087650155810994767419384564270272155507498603819968
e = 65537
n = 23952937352643527451379227516428377705004894508566304313177880191662177061878993798938496818120987817049538365206671401938265663712351239785237507341311858383628932183083145614696585411921662992078376103990806989257289472590902167457302888198293135333083734504191910953238278860923153746261500759411620299864395158783509535039259714359526738924736952759753503357614939203434092075676169179112452620687731670534906069845965633455748606649062394293289967059348143206600765820021392608270528856238306849191113241355842396325210132358046616312901337987464473799040762271876389031455051640937681745409057246190498795697239

q = n // p
totient = (p-1) * (q-1)
d = pow(e, -1, totient)
plaintext = pow(ciphertext, d, n)
# plaintext = 218378661235194013475375491560393839271890611748313869466505982183260263630681999229565
```

---

## Extracting the Flag

The server hints that the plaintext is ASCII-encoded as a big integer. Convert it:

```python
n = 218378661235194013475375491560393839271890611748313869466505982183260263630681999229565
print(bytes.fromhex(hex(n)[2:]).decode())
```

```
picoCTF{wA8_th4till3aGal..ob6435DeB}
```

---

## Key Takeaways

1. **RSA security depends on not knowing `p` and `q`** — if an attacker gets either prime, the entire scheme collapses.
2. **Feasibility matters** — some operations are mathematically possible but computationally infeasible (factoring large `n`).
3. **Knowing just one prime factor is enough** — Problem 8 shows that having `p` alone lets you recover everything.
4. **Plaintexts can be encoded as integers** — ASCII strings are just big numbers in a different base.

---

**Flag:** `picoCTF{wA8_th4till3aGal..ob6435DeB}`
