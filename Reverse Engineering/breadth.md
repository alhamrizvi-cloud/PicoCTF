

## 1. The Problem: Too Many Flags
If you run `strings` on the files, you'll see hundreds of fake flags. This is "horizontal scaling" in a literal, troll-y sense—the developer just packed the binary with thousands of functions, each printing a different fake flag. 

Since the program always exits with "Goodbye!", none of these functions are actually being called. They are **dead code**.

## 2. The Strategy: Binary Diffing
Instead of reverse engineering thousands of functions, we compare the "broken" version (`v1`) with the "fixed" version (`v2`). The function that changed is almost certainly the one containing the real flag.

### Step A: Finding the Difference
We use `radiff2` to see exactly which bytes are different between the two files.

```bash
radiff2 breadth.v1 breadth.v2
```

The output shows a few changes, but one stands out at offset `0x0009504b`. This is where the logic actually changed, rather than just metadata like the Build ID.

## 3. The Solution: Deep Dive with Radare2
Now, we open the **fixed version (`v2`)** in `r2` and jump to that specific address to see what the "fix" was.

### Step B: Inspecting the Code
1.  **Open and Analyze:** `r2 ./breadth.v2` then run `aa` (analyze all).
2.  **Seek to the address:** `s 0x0009504b`
3.  **Disassemble:** `pdb` (print disassemble block).

In the assembly, we see a function `sym.fcnkKTQpF`:
* It moves a value (`0x41bc73e`) into a register.
* It compares (`cmp`) that register against the same value.
* It jumps if equal (`je`) to a specific address: `0x95060`.

### Step C: Following the Jump
When we look at the code at `0x95060`, we see:
```asm
lea rdi, str.picoCTF{VnDB2LUf1VFJkdfDJtdYtFlMexPxXS6X}
jmp sym.imp.puts
```

This function loads a specific string into `rdi` and calls `puts` to print it. Because this was the only functional change between the "broken" and "fixed" versions, this must be the real flag.

## Final Flag
**`picoCTF{VnDB2LUf1VFJkdfDJtdYtFlMexPxXS6X}`**

---

## Summary for Beginners
* **Horizontal Scaling:** A joke about the binary having a massive amount of identical-looking functions.
* **Dead Code:** Functions that exist in the file but are never executed by the `main` loop.
* **Diffing:** The fastest way to find a needle in a haystack when you have two slightly different haystacks.
