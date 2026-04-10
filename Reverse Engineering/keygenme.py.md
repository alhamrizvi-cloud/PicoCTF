## Step 1: Understand the key format

From the code:

```
key_part_static1_trial = "picoCTF{1n_7h3_kk3y_of_"
key_part_dynamic1_trial = "xxxxxxxx"
key_part_static2_trial = "}"
```

So the key format is:

```
picoCTF{1n_7h3_kk3y_of_XXXXXXXX}
```

The last 8 characters are dynamic.

---

## Step 2: Find how dynamic part is generated

Inside `check_key()`:

```
hashlib.sha256(username_trial).hexdigest()
```

Username is:

```
b"BENNETT"
```

Then specific characters are taken from the hash in this order:

```
[4], [5], [3], [6], [2], [7], [1], [8]
```

---

## Step 3: Compute SHA256 hash

Run:

```
python3 -c "import hashlib; print(hashlib.sha256(b'BENNETT').hexdigest())"
```

Output:

```
ba6c084a4d888e1f7c3b0fc71d61c4625708bd915b5e0e60eb73e1667251b567
```

---

## Step 4: Extract required characters

Indexing the hash:

```
b a 6 c 0 8 4 a 4 ...
0 1 2 3 4 5 6 7 8
```

Pick characters:

```
[4] = 0
[5] = 8
[3] = c
[6] = 4
[2] = 6
[7] = a
[1] = a
[8] = 4
```

Dynamic part:

```
08c46aa4
```

---

## Step 5: Final key

```
picoCTF{1n_7h3_kk3y_of_08c46aa4}
```

---

## Step 6: Use the key

Run the program:

```
python3 key.py
```

Choose option:

```
c
```

Enter the key:

```
picoCTF{1n_7h3_kk3y_of_08c46aa4}
```

---

## Step 7: Final step

* Program creates `keygenme.py`
* Run it:

```
python3 keygenme.py
```

This gives the final output or flag.
