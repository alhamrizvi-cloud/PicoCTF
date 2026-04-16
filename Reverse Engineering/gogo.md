

We are given a binary (`enter_password`) and a remote service. The goal is to find:

* The correct password
* The unhashed key

## Step 1: Reverse Engineering

Opened the binary in Ghidra and found a function:

```c
checkPassword()
```

Inside it, the program checks the password using this logic:

```
input[i] ^ secret[i] == expected[i]
```

* Loop runs 32 times (0x20), so password length is 32
* Uses XOR operation

## Step 2: Debugging with GDB

Started GDB:

```bash
gdb ./enter_password
```

Set breakpoint at XOR instruction:

```bash
b *0x080d4b28
```

Run program:

```bash
r
```

Entered 32 'a' characters.

## Step 3: Extract values

From registers:

Input:

```bash
x/32bx $ecx
```

Secret array:

```bash
x/32bx $esp+0x4
```

Expected array:

```bash
x/32bx $esp+0x24
```

## Step 4: Recover password

Using:

```
input = secret ^ expected
```

Python script:

```python
secret = [...]
expected = [...]

password = ''.join(chr(s ^ e) for s,e in zip(secret, expected))
print(password)
```

Password:

```
reverseengineericanbarelyforward
```

---

## Step 5: Find unhashed key

Secret array converted to ASCII:

```
861836f13e3d627dfa375bdb8389214e
```

This is an MD5 hash.

Cracked using an online tool, result:

```
goldfish
```

## Step 6: Connect to service

```bash
nc wily-courier.picoctf.net 62523
```

Enter:

```
reverseengineericanbarelyforward
goldfish
```

## Flag

```
picoCTF{p1kap1ka_p1c0b187f1db}
```

## Key Takeaways

* XOR logic can be reversed easily
* GDB helps extract runtime values
* Hashes like MD5 can often be cracked using public databases
