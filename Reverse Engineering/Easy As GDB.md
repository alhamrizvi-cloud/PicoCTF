## Step 1: Run the binary

When we run the file:

```
./brute67
```

It asks:

```
input the flag:
```

After entering anything, it prints:

```
checking solution...
Incorrect.
```

So the program is checking our input against some hidden value.

---

## Step 2: Open in Ghidra

Open the binary in Ghidra and look for the main function.
The important function is:

```
FUN_000109af
```

It looks like this:

```c
__s = calloc(0x200,1);
printf("input the flag: ");
fgets(__s,0x200,_stdin);

sVar1 = strnlen(&DAT_00012008,0x200);

uVar2 = FUN_0001082b(__s,sVar1);
FUN_000107c2(uVar2,sVar1,1);

iVar3 = FUN_000108c4(uVar2,sVar1);
```

---

## Step 3: Understand the logic

The program does:

1. Takes input
2. Gets length using `DAT_00012008`
3. Applies some transformation (XOR + shuffle)
4. Compares with `DAT_00012008`

So:

```
DAT_00012008 = encoded flag
```

---

## Step 4: Locate the encoded flag

In Ghidra, go to address:

```
DAT_00012008
```

You will see bytes like:

```
7a 2e 6e 68 1d 65 16 7c 6d 43 
6f 36 36 62 1a 45 43 32 40 61 
58 01 58 65 62 66 53 30 3b 17
```

This is the encrypted and shuffled flag.

---

## Step 5: Reverse the logic

We reverse what the program does.

### 1. Reverse the shuffle

```python
for i in range(len(dest)-1, 0, -1):
    for j in range(0, len(dest)-i+1, i):
        dest[j], dest[j+i-1] = dest[j+i-1], dest[j]
```

---

### 2. Reverse the XOR

```python
for i in range(180154381, 0xDEADBEEF, 2075469):
    v5 = [
        (i >> 24) & 0xff,
        (i >> 16) & 0xff,
        (i >> 8) & 0xff,
        i & 0xff
    ]
    for j in range(len(dest)):
        dest[j] ^= v5[j % 4]
```

---

### 3. Convert to string

```python
print("".join(chr(x) for x in dest))
```

---

## Step 6: Final flag

```
picoCTF{I_5D3_A11DA7_e5458cbf}
```

---

## Key Idea

Instead of brute forcing with GDB, we:

* Found the encoded flag in memory
* Reversed the operations (shuffle + XOR)
* Recovered the original flag

This is faster and cleaner than brute force.
