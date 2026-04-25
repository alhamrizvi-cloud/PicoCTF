# rsa_oracle — picoCTF 2024 Solution (Simple Writeup)

## 1. Problem Idea

You are given:

* `password.enc` → RSA encrypted password
* `secret.enc` → AES encrypted file
* RSA oracle at `nc titan.picoctf.net 62026`

The oracle can:

* Encrypt anything (E)
* Decrypt anything (D)

BUT it has a restriction:

> It will NOT decrypt the exact password ciphertext

## 2. Key Concept (Very Important)

This challenge is based on:

> RSA homomorphic property

That means:

[
(c_1 \cdot c_2)^d = m_1 \cdot m_2
]

So multiplication in ciphertext space becomes multiplication in plaintext space after decryption.


## 3. Attack Idea (Blinding Trick)

We “trick” the oracle into decrypting something slightly different.

## Step 1: Encrypt a small number

We choose:

```text id="step1"
2
```

Send to oracle:

```
E → 2
```

Oracle returns:

```text id="c1"
c_a = encrypt(2)
```

## Step 2: Multiply ciphertexts

We already have:

```text id="c2"
c = password.enc
```

Now compute:

```text id="mul"
c' = c * c_a
```


## Step 3: Decrypt modified ciphertext

Send:

```
D → c'
```

Oracle returns:

```text id="out1"
2 * password
```

Because:

* encryption of 2 was multiplied in
* oracle decrypts product due to RSA property


## Step 4: Remove blinding factor

Now divide:

```text id="final1"
password = (2 * password) / 2
```

So you recover original password.


## 4. Why this works

Because RSA is:

* multiplicative
* deterministic (no padding here)
* vulnerable when oracle exists

So:

```text id="math1"
D(c * E(2)) = 2 * m
```

Then:

```text id="math2"
(2 * m) / 2 = m
```

## 5. Final Step — Decrypt secret file

Now use recovered password:

```bash id="finalcmd"
openssl enc -aes-256-cbc -d -in secret.enc -k <password>
```

This reveals the flag.

## 6. Why oracle restriction fails

The oracle blocks only:

* exact `password.enc`

But we send:

* `password.enc * encrypt(2)`

So it looks different, but still contains same information.

## 7. Key Insight

This is NOT breaking RSA mathematically.

It is:

> abusing RSA’s algebraic structure through an oracle

## 8. Summary Flow

```text id="flow1"
password.enc
   ↓
multiply with encrypt(2)
   ↓
send to oracle
   ↓
get 2 × password
   ↓
divide by 2
   ↓
recover password
   ↓
decrypt secret.enc
```

## 9. Final Understanding

This challenge teaches:

* RSA is malleable without padding
* oracles leak information indirectly
* encryption must include padding (OAEP) to be secure
