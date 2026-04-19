This challenge is a great example of how to use **GDB Python scripting** to "brute force" a flag when the math is too annoying to reverse by hand.

## 1. The Goal
We are given a program (`otp`) and an encrypted string. To get the flag, we need to find a **100-character key** that the program likes. Once we have that key, we XOR it with the encrypted string to reveal the flag.

## 2. The Problem: The "Jumble"
When you type a key into the program, it doesn't just check it. It "jumbles" it:
* The first character you type is scrambled.
* Every character after that is scrambled **based on the previous character’s result**.
* Finally, the program adds the letter `'a'` to everything and checks if it matches a hardcoded 100-character string: `mlaebfkoib...`

Because each character depends on the one before it, we can't just guess the whole 100-character key at once. But we **can** guess them one by one.

## 3. The Strategy: The "Checkpoint"
Instead of doing the math, we use GDB (the debugger) to do the work for us. We set a **breakpoint** at the exact moment the program finishes jumbling a character.

### The Breakpoint Logic (`0x55555555499d`)
We tell GDB: *"Stop right here. Look at the memory where the jumbled character is stored, and tell me what it is."*

### The Script Logic
We wrote a Python script that runs inside GDB:
1.  **Try a character:** It runs the program with `key + '0'`.
2.  **Check the result:** The breakpoint triggers and tells the script: *"The jumbled version of that character is 'x'."*
3.  **Compare:** If 'x' matches the character we need (from `mlaebfkoib...`), we keep it and move to the next position.
4.  **Repeat:** If it doesn't match, we try `'1'`, then `'2'`, and so on.

## 4. The Final Step: XOR
After the script runs for a few minutes, it finds all 100 characters of the key. 

* **Key found:** `6fa2e2a25c3b5869...`
* **Ciphertext:** `1fcb81cd1f6f1e12...`

When you XOR these two hex strings together, the "jumble" cancels out, and the ASCII text appears.

## Final Result
**Flag:** `picoCTF{cust0m_jumbl3s_4r3nt_4_g0Od_1d3A_ca692500}`
