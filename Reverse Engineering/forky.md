
We are given a binary and asked to reverse it using Ghidra. The goal is to find the final integer passed to the function `doNothing()`.

### Step 1: Understand the Code

Main function:

```c
piVar1 = mmap(...);
*piVar1 = 1000000000;

fork();
fork();
fork();
fork();

*piVar1 = *piVar1 + 0x499602d2;

doNothing(*piVar1);
```

What’s happening:

* A number is stored: `1000000000`
* The program calls `fork()` 4 times
* Then it adds `0x499602d2`
* Then sends the result to `doNothing()`

Each `fork()` doubles the number of processes.

So:

* 1st fork → 2 processes
* 2nd fork → 4 processes
* 3rd fork → 8 processes
* 4th fork → 16 processes

Final: **16 processes running**

Each process executes:

```c
*piVar1 = *piVar1 + 0x499602d2;
```

So total addition:

```id="calc1"
1000000000 + (16 × 0x499602d2)
```

C uses **32-bit integers**, so the value may overflow.

We simulate this in Python:

```python id="calc2"
import ctypes
ctypes.c_int32(1000000000 + 16*0x499602D2)
```

Output:

```id="calc3"
-721750240
```

```id="calc4"
picoCTF{-721750240}
```

### Key Idea

* `fork()` multiplies processes
* Each process runs the same code
* Total effect = number of processes × operation
* Always consider **integer overflow in C**
