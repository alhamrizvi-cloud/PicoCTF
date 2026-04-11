

After downloading the file, I realized it was an `objdump` of a Python script. When we open the file, we can see a lot of constants defined in it. These constants are loaded to build a list called `input_list`.

Then, a `key_str` is defined, which eventually becomes `t_Jo3`.

Looking further down, we can see that the program performs an XOR operation between the values of `input_list` and a repeating key.

Above that section, there are many `LOAD_CONST` values — these are actually the ciphertext stored in decimal form:

```
4 54 41 0 112 32 25 49 33 3 0 0 57 32 108 23 48 4 9 70 7 110 36 8 108 7 49 10 4 86 43 102 126 92 0 16 58 41 89 78
```

At first, I tried converting these decimal values directly to characters, but it produced random output.

Then I tried XORing them using a UTF-8 approach, which required a key. I initially used `j_o3t`, but it still gave incorrect results.

So instead, I used a known flag prefix `picoCTF{` as the key for XOR. From the output, I was able to recover the correct repeating key, which turned out to be:
<img width="1348" height="504" alt="image" src="https://github.com/user-attachments/assets/923e53d9-9dbb-4860-a401-cfa34460c8c6" />

```
t_Jo3
```

Finally, I used this key to XOR the ciphertext properly and successfully recovered the flag.

---

If you want, I can also make this into a **perfect CTF writeup (GitHub/Medium level)** with code + explanation.
