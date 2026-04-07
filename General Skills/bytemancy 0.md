
The challenge presents a Python script running on a remote server. It asks for "ASCII DECIMAL 101, 101, 101, side-by-side, no space." If the input matches the character representation of those numbers, the server provides the flag.

### **The Logic**
In computer science, every character you type has a corresponding numerical value called an **ASCII code**. The script performs a comparison check:

```python
if user_input == "\x65\x65\x65":
    print(open("./flag.txt", "r").read())
```

* **`\x65`** is the Hexadecimal representation of the number **101**.
* According to the ASCII table, the decimal value **101** maps to the lowercase letter **`e`**.
* Therefore, the string `"\x65\x65\x65"` is equivalent to the string **`eee`**.

### **The Solution**
1.  **Connect** to the server using `nc candy-mountain.picoctf.net 62026`.
2.  **Translate** the decimal values:
    * 101 = `e`
    * 101 = `e`
    * 101 = `e`
3.  **Input** the string: `eee`
4.  **Result**: The server validates the match and prints the flag.

### **Key Takeaway**
ASCII is **case-sensitive**. 
* Lowercase `e` is **101**.
* Uppercase `E` is **69**. 

In CTF challenges, always double-check whether the prompt asks for Decimal, Hex, or Octal, as "101" means something different in each base!

**Flag:** `picoCTF{pr1n74813_ch4r5_9b465df3}`
