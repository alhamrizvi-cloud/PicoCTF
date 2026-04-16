

We are given a binary (`chall`) and need to find the correct input (flag). Static inspection shows complex operations, so we use **dynamic analysis with GDB**.

---

## Step 1: Analyze the binary

Opened the binary in GDB:

```bash
gdb ./chall
```

Disassembled `main`:

```bash
disas main
```

Observed:

* Input is taken using `fgets`
* Some transformations (XOR operations) are applied
* Final comparison is done using `memcmp`

Key instruction:

```asm
0x0000000000400623 <+381>: call   0x4003a0 <memcmp@plt>
```

---

## Step 2: Set breakpoint at correct location

Instead of breaking on all `memcmp` calls (which includes libc noise), set breakpoint at the exact instruction:

```bash
b *0x400623
```

---

## Step 3: Run the program

```bash
r
```

Entered any input:

```
a
```

---

## Step 4: Inspect registers at breakpoint

When execution stops at the breakpoint, inspect arguments passed to `memcmp`.

On x86_64:

* `$rdi` → first argument (input)
* `$rsi` → second argument (expected value)
* `$rdx` → length

Printed the second argument:

```bash
printf "%s\n", $rsi
```

---

## Step 5: Extract the flag

Output:

```
picoCTF{dyn4m1c_4n4ly1s_1s_5up3r_us3ful_039b10e}
```

This is the correct flag.

---

## Flag

```
picoCTF{dyn4m1c_4n4ly1s_1s_5up3r_us3ful_039b10e}
```
