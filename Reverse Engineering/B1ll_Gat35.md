I first tried debugging on a Windows 7(fuh because why tf you added a windows rev engineering cjallenge) setup using rpcclient, 
but the debugger(Ollydbg) failed to work, because i was fking using rpcclient and yes a dedass windows7 wasting on about an hour, I then moved to my Ghidra, where I was able to proceed successfully.

From Ghidra:

* Takes a number input
* Asks for a **key**
* Checks the key using a function
* If correct → prints flag
* If wrong → says incorrect

### Important part:

```c id="b1"
if (check_key(input) == 0)
    print("Incorrect")
else
    print("Correct") + show_flag()
```

So:

> If function returns **0 → wrong**
> If **non-zero → correct**

## Step 2: Easy Idea (Bypass)

Instead of finding the key, we **force the program to always go to "Correct"**


## Step 3: Patch the binary

In assembly:

```id="b2"
TEST EAX, EAX
JNZ correct_block
```

### Meaning:

* `JNZ` → jump if NOT zero (correct case)

### Trick

Change:

```id="b3"
JNZ → JNC
```

Why?

* `TEST` sets Carry Flag (CF) = 0
* `JNC` = Jump if CF = 0
* So it will **ALWAYS jump to correct block**

## Step 4: Run patched binary

```bash id="b4"
wine patched.exe
```

Input anything:

```id="b5"
Correct input. Printing flag:
PICOCTF{These are the access codes to the vault: 1063340}
```

