
We are given a binary and a file `rev_this`.

When we open `rev_this`, we see something like:

```id="a1"
picoCTF{w1{1wq8rkgkuY@L}
```

From reversing in Ghidra:

1. Reads the real flag from `flag.txt`
2. Writes a modified version into `rev_this`


### First 8 characters

```id="a2"
same as original
```

---

### Middle part (index 8 → 22)

```id="a3"
if index is even → +5
if index is odd  → -2
```

---

### Last character

```id="a4"
same as original
```

---

## Goal

We have encoded string → we need original

So we **reverse operations**:

| Case       | Encoding | Decoding |
| ---------- | -------- | -------- |
| even index | +5       | -5       |
| odd index  | -2       | +2       |


##  Solve Script

```python id="a6"
flag = ""

with open("rev_this", "rb") as data:
    data = data.read()

# first 8 same
for i in range(8):
    flag += chr(data[i])

# reverse logic
for i in range(8, 23):
    if i % 2 == 0:
        flag += chr(data[i] - 5)
    else:
        flag += chr(data[i] + 2)

# last char
flag += chr(data[23])

print("Flag:", flag)
```
