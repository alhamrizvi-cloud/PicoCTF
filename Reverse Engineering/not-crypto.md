
## Challenge Overview
* **Category:** Reverse Engineering
* **Points:** 150
* **The Hook:** The description says there is "crypto" inside, but the challenge isn't actually about cryptography. This is a hint that we shouldn't waste time math-modeling the encryption.

---

## Step 1: Initial Triage
When we run the binary, it asks for a "bargain" (input) and tells us "Nope" if we get it wrong. 

By opening the file in **Ghidra**, we see a `main` function (renamed `FUN_00101070`) that looks like "spaghetti code." It has dozens of variables and complex loops. However, two lines stand out at the very end of the logic:

1.  `fread(user_input, 1, 0x40, stdin);` — The program takes 64 bytes of input from us.
2.  `iVar24 = memcmp(local_88, user_input, 0x40);` — This is the "Eureka" moment. 

The program takes your input, does a bunch of complex math (the "not crypto" part), and then uses `memcmp` (memory compare) to see if your input matches a string it calculated in the background.

---

## Step 2: Setting up the Debugger
Instead of calculating what `local_88` is by hand, we can just let the program calculate it for us and peek at its memory right before the comparison.

### 1. Find the Address
Because of **ASLR** (Address Space Layout Randomization), the program moves around in memory. We use `gdb` with the `gef` extension:
* Run `starti` to catch the program at the very beginning.
* Run `vmmap` to see where the program is loaded (e.g., `0x0000555555554000`).

### 2. Match Ghidra to GDB
In Ghidra, we go to **Window -> Memory Map** and set the "Home" icon (Base Address) to match the address we found in GDB. Now, the addresses in our decompiler match our debugger.

### 3. Set a Breakpoint
We find the exact address of the `memcmp` call. In this case, it was `0x5555555553b9`.
```bash
gef> b *0x5555555553b9
```

---

## Step 3: Extracting the Flag
Now, we run the program (`r`) and type in a bunch of "A"s as dummy input.



When the program hits our breakpoint, it stops right before comparing our input to the real flag. In Linux x64 calling conventions:
* **RDI:** Holds the first argument (The correct flag string).
* **RSI:** Holds the second argument (Our dummy "A"s).

We simply look at the **RDI** register. It contains a pointer to the memory where the flag is stored. We can print it as a string:

```bash
gef> printf "%s", $rdi
picoCTF{c0mp1l3r_0pt1m1z4t10n_15_pur3_w1z4rdry_but_n0_pr0bl3m?}
```

---

## Summary
The challenge tried to distract us with "fake" cryptography—a messy mess of XORs and bit shifts. By identifying the **check point** (`memcmp`), we bypassed the math entirely and let the program reveal its secrets. 

**Flag:** `picoCTF{c0mp1l3r_0pt1m1z4t10n_15_pur3_w1z4rdry_but_n0_pr0bl3m?}`
