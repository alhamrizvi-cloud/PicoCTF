# 📝 Easy1 PicoCTF : Vigenère Cipher Challenge

## 🧩 Challenge Given

We were given:

* **Ciphertext:** `UFJKXQZQUNB`
* **Key:** `SOLVECRYPTO`
* A Vigenère table (alphabet shift table)

The hint said this is related to a **one-time pad**, but since the **key is known**, we can decrypt it easily.

---

## 🔎 Step 1: Understand the Cipher

The table provided is a **Vigenère cipher table**.

In Vigenère:

* Encryption = Add key shift
* Decryption = Subtract key shift

Since we have the key, we will **decrypt**.

---

## 🔑 Step 2: Align Key with Ciphertext

Ciphertext:

```
UFJKXQZQUNB
```

Key (same length):

```
SOLVECRYPTO
```

Write them together:

```
Cipher: U  F  J  K  X  Q  Z  Q  U  N  B
Key:    S  O  L  V  E  C  R  Y  P  T  O
```

---

## 🔓 Step 3: Decrypt (Simple Explanation)

For each letter:

👉 Find the **row of the key letter**
👉 Look for the **cipher letter in that row**
👉 The column letter is the **original plaintext**

OR

👉 Just subtract alphabet positions
(A=0, B=1, C=2 … Z=25)

Example:

* U − S = C
* F − O = R
* J − L = Y

Continue for all letters.

---

## ✅ Final Result

After decrypting all letters:

```
CRYPTOISFUN
```

Formatted properly:

# 🎉 **CRYPTO IS FUN**

---

## 💡 Why It Was Easy

A real **One-Time Pad** is secure only if:

* The key is random
* The key is secret
* The key is never reused

Here, the key was given (`SOLVECRYPTO`), so the cipher becomes easy to break.

---

If you want, I can also give you a **short 4–5 line version** for quick CTF submission.
