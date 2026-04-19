I spent almost 2 hours on this challenge. I wasted a lot of time using AI to analyze random functions and strings, and I still don’t really understand these hard-tier challenges, But at least I got the flag in about 3 hours, because getting a proper writeup is almost impossible when fewer than 350 people have solved it lol

This challenge is NOT about fully reversing the binary.

Instead, we abuse a **timing side channel**.

👉 Meaning:  
We don’t solve the logic…  
We observe **how long the program runs**.

## 🔍 Step 1: First Look (Ghidra)

Binary is written in Rust → decompiled code looks messy.

Like this:

```c
/* WARNING: Removing unreachable block ... */
void FUN_00105960(void) {
   ...
}
````

👉 ignore the mess.

##  Find Important Strings

From strings / Ghidra:

```
Invalid length
Invalid password
Success
picoCTF{
```

So we know:

* It checks length first
* Then checks password
* Flag format is standard

## Step 2: Basic Checks

From reversing:

```c
len(argv[1]) == 41
```

total length = 41

Also:

```c
*plVar3 == 0x7b4654436f636970
```

Reverse that:

```
{FTCocip → picoCTF{
```

So flag is:

```
picoCTF{????????????????????????????????}
          (32 chars inside)
```

---

## Step 3: How Password is Checked

Inside binary → big chain of `if` conditions:

```asm
MOV → CMP → JNZ → CMP → JNZ ...
```

means:

* it checks **byte by byte**
* stops early if something is wrong

The check looks like this:

```python
def check(password):
    for i in range(len(password)):
        if password[i] != correct[i]:
            return False
    return True
```

👉 Problem:

* If first char is correct → loop runs longer
* If more chars are correct → runs even longer

## 🧠 Timing Side Channel (Core Idea)

We don’t guess randomly.

We do this:

1. Try a character
2. Measure execution time
3. If time increases → correct guess

### Example

Correct password = `48571243`

Instead of brute force:

```
00000000
00000001
...
```

We do:

```
10000000 → longer? yes → first char = 1
12000000 → longer? yes → second char = 2
...
```

👉 reduces complexity A LOT

## Step 4: Measuring Time (Using Valgrind)

We use:

👉 Valgrind

Specifically:

* `cachegrind`

It gives:

```
I refs = number of instructions executed
```

👉 more instructions = more correct characters

## ⚙️ Attack Strategy

We build flag step by step.

### Stage 1 — Find correct character

Try same char everywhere:

```
aaaa....
bbbb....
cccc....
```

Check which one gives higher instruction count.

👉 that char exists in flag

### Stage 2 — Find position

Now place that char at different positions:

```
a~~~~~~~
~a~~~~~~
~~a~~~~~
```

👉 correct position = highest instruction count

## 🧾 Exploit Script (Clean Version)

```python
from pwn import *
import string

VALGRIND = "valgrind"
CHARS = string.printable[:-6]

def measure(inp):
    p = process([
        VALGRIND,
        "--tool=cachegrind",
        "--cachegrind-out-file=/dev/null",
        "./checkpass",
        "picoCTF{" + inp + "}"
    ])
    p.recvuntil(b'I   refs:')
    val = int(p.recvline().strip().replace(b",", b""))
    p.close()
    return val

flag = ["~"] * 32
best = 0
found = []

while "~" in flag:

    # Stage 1
    for c in CHARS:
        guess = [c]*32
        for ch, idx in found:
            guess[idx] = ch
        val = measure("".join(guess))

        if val > best:
            best = val
            current = c
            break

    # Stage 2
    for i in range(32):
        temp = flag.copy()
        temp[i] = current

        val = measure("".join(temp))
        if val > best:
            best = val
            flag[i] = current
            found.append((current, i))
            break

    print("Current:", "".join(flag))
```

## 🏁 Final Flag

```
picoCTF{t1mingS1deChann3l_gVQSfJxl3VPFGQ}
```


Bad comparison:

```python
if x != y: return False
```

Good (safe):

```python
result = True
for x,y in zip(a,b):
    if x != y:
        result = False
return result
```
