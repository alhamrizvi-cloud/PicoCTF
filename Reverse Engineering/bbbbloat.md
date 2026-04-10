
## Challenge Writeup

The program prompts the user with:

```
What's my favorite number?
```

### Step 1: Static Analysis in Ghidra

Open the binary in **Ghidra** and let it analyze the file.

Navigate to:

```
Window → Defined Strings
```

From the list of strings, locate:

```
"What's my favorite number?"
```

### Step 2: Cross References

Click on the string and view its **cross references (XREF)**. This leads to the function where the string is used (likely the `main` function).

### Step 3: Decompilation

In the decompiler view, the logic becomes clear:

* A variable (e.g., `local_48`) stores user input.
* The program compares this input against a hardcoded value:

  ```
  0x86187
  ```

### Step 4: Convert Hex to Decimal

Convert the hexadecimal value:

```
0x86187 = 549255
```

### Step 5: Run the Program

Provide the correct input:

```
./bbbbloat
What's my favorite number? 549255
```

### Step 6: Flag

The program returns the flag:

```
picoCTF{cu7_7h3_bl047_36dd316a}
```

---

## Final Answer

* Favorite number: **549255**
* Flag: **picoCTF{cu7_7h3_bl047_36dd316a}**
