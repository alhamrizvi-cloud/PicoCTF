<img width="1083" height="392" alt="image" src="https://github.com/user-attachments/assets/38110c96-41bb-45bf-8613-60b421e8a172" />


```
enc = open("level3.flag.txt.enc", "rb").read()
```

You open the encrypted file in **binary mode (`rb`)** and read all its contents into the variable `enc`. This gives you raw bytes instead of text, which is important because encryption works at the byte level.

```
known = b"picoCTF{"
```

Here you define a **known plaintext prefix**. PicoCTF flags always start with `picoCTF{`, so you’re using that as a reference to recover the key.

```
key = bytes([a ^ b for a, b in zip(enc, known)])
```

This is the key step. You:

* Pair each byte of the ciphertext (`enc`) with each byte of the known plaintext (`known`) using `zip`
* XOR (`^`) them together

Since encryption was likely done as:

```
cipher = plaintext ^ key
```

You reverse it as:

```
key = cipher ^ plaintext
```

This gives you the **first part of the repeating XOR key**, which turns out to be:

```
b'22952295'
```

```
print(key)
```

Just prints the recovered key so you can see it.

```
decrypted = bytes([c ^ key[i % len(key)] for i, c in enumerate(enc)])
```

Now you decrypt the entire ciphertext:

* `enumerate(enc)` gives you both the index `i` and byte `c`
* `key[i % len(key)]` repeats the key cyclically
* Each ciphertext byte is XORed with the corresponding key byte

This reconstructs the original plaintext.

```
print(decrypted.decode())
```

Finally, you convert the decrypted bytes into a readable string using `.decode()` and print it.

```
picoCTF{m45h_fl1ng1ng_6f98a49f}
```
