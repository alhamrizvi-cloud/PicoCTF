

## Easy Method (Using `strings`)

First, we can quickly check the binary using:

```bash
strings binary
```

This reveals readable strings inside the binary, and we can directly spot the flag:

```
picoCTF{3lf_r3v3r5ing_succe55ful_fa9cb3b}
```

👉 So yes, the flag can be obtained easily using `strings`.

##  Actual Reverse Engineering Method (GDB)

To properly understand the binary, I analyzed it using GDB.

### Step 1: Find main

```bash
info functions
```

Then:

```bash
disas main
```

---

### Step 2: Analyze main

Inside `main`, I found multiple `movabs` instructions loading large hex values:

```asm
movabs $0x7b4654436f636970,%rax
movabs $0x337633725f666c33,%rdx
movabs $0x75735f676e693572,%rax
movabs $0x6c75663535656363,%rdx
movabs $0x623362633961665f,%rax
```

These values are stored in memory and later used in:

```asm
call strcmp
```

👉 Meaning:
The program compares user input with this hidden string.

---

### Step 3: Convert Hex → ASCII

Since the system is **little endian**, we reverse the bytes and convert to ASCII.

Example:

```
0x7b4654436f636970 → picoCTF{
```

After converting all parts and combining them:

```
picoCTF{3lf_r3v3r5ing_succe55ful_fa9cb3b}
```

---

## 🎯 Final Flag

```
picoCTF{3lf_r3v3r5ing_succe55ful_fa9cb3b}
```

---

## 💡 Conclusion

* The flag was hardcoded inside the binary
* It could be extracted easily using `strings`
* But proper reversing involved:

  * Disassembling `main`
  * Identifying `strcmp`
  * Decoding hex values manually
