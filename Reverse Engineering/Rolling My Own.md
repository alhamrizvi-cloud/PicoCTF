
When we run the program:

```bash
./remote
```

and enter any password:

```
Password: a
Illegal instruction (core dumped)
```

👉 This means the program is **executing bad machine code**.

* Your input (password) is used to **generate machine code**
* That machine code is then **executed**
* If password is wrong → garbage code → crash
* If password is correct → valid code → prints flag

## Step 1: Understand main logic

From Ghidra:

### What program does:

1. Takes password input
2. Splits it into 4 parts (each 4 characters)
3. Adds a **salt** after each part
4. Creates a big string

Example idea:

```
[part1 + salt1] + [part2 + salt2] + ...
```

## Step 2: Hashing

* The big string is split into chunks
* Each chunk → **MD5 hash**
* So we get 4 MD5 hashes

---

## Step 3: Build shellcode

Program does:

* Takes specific bytes from each MD5
* Combines them → makes **16 bytes of shellcode**
* Then runs it using:

```c
(*(code *)puVar2)(FUN_0010102b);
```

---

## Step 4: What should shellcode do?

Look at this function:

```c
FUN_0010102b
```

It prints flag ONLY if:

```c
param_1 == 0x7b3dc26f1
```

👉 That means:

* Shellcode must call this function
* AND pass correct value in `rdi`

## 💡 Required shellcode (simple idea)

It should do:

```asm
mov rsi, rdi        ; save function pointer
mov rdi, 0x7b3dc26f1
call rsi
ret
```

## Step 5: Convert shellcode to bytes

Final bytes:

```
48 89 FE 48 BF F1 26 DC B3 07 00 00 00 FF D6 C3
```

---

## Step 6: Reverse the process

We know:

* Program builds shellcode from MD5 outputs
* So we reverse it

We brute-force password so that:

```
MD5(password + salt) → gives required bytes
```

## Step 7: Use hint

Hint says:

```
Password starts with D1v1
```

So:

```
D1v1????????
```

## Step 8: Brute force remaining parts

We brute-force remaining 3 parts so that MD5 matches required bytes.

Final password found:

```
D1v1d3AndC0nqu3r
```

## Step 9: Get flag

```bash
nc mercury.picoctf.net 23773
```

Enter:

```
D1v1d3AndC0nqu3r
```

## Flag

```
picoCTF{r011ing_y0ur_0wn_crypt0_15_h4rd!_3c22f4e9}
```

## Key Takeaways

* Input → affects executed code
* Wrong input → crash
* Correct input → valid shellcode
* MD5 used to hide logic
* Reverse + brute force = solution
