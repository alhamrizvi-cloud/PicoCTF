Alright, here’s your **clean, simple writeup in easy language** 👇 (no overcomplicated stuff)

---

# 📝 CTF Writeup – Mac Time Stego (Super Simple)

## 🔍 Step 1: Run the file

When we run `main.exe`, we get:

```
No flag found, please make sure this is run on the server
```

👉 This means:

* Program is trying to read `flag.txt`
* But we don’t have it

So we create a fake one:

```bash
echo picoCTF{fake_flag} > flag.txt
```

Run again:

```
Work is done!
Wait for 5 seconds to exit.
```

👉 But nothing visible happens ❌

---

## 🧠 Step 2: Think – what is it doing?

We check files → nothing changed
So we reverse the binary using **Ghidra**

---

## 🔬 Step 3: Analyze main()

Main function does:

* Opens `flag.txt`
* Reads **18 characters**
* Stores it in memory
* Calls `_listdir("./test")`

👉 So flag is being used somewhere inside `test/` folder

---

## 📂 Step 4: Analyze `_listdir`

This function:

* Goes through all files in `./test`
* For **every alternate file**
* Calls `_hideInFile()`

👉 Important:

* Not all files used ❗
* Only **every second file**

---

## 🕵️ Step 5: Analyze `_hideInFile`

This is the most important part.

It:

* Opens a file
* Reads its **timestamps**
* Modifies them using flag data

Specifically:

```c
_encodeBytes(c1, c2, &last_write_time);
```

👉 Means:

* 2 characters of flag stored in timestamp

---

## 💡 Key Concept

Windows file timestamps (MAC times):

* **M** = Modified
* **A** = Accessed
* **C** = Created

Stored in **very high precision (100 nanoseconds)**

👉 So flag is hidden inside **file time values**

---

## 🔥 Step 6: Find hidden decode function

We find an unused function: `_decode`

It:

* Reads timestamps
* Extracts bytes
* Prints flag

But it is **never called normally**

---

## ⚔️ Step 7: Use debugger trick

Using debugger (like **x64dbg**):

1. Set breakpoint at:

   ```
   0x00401bda
   ```

2. Run program until breakpoint

3. Change instruction pointer:

   ```
   EIP = 0x00401afe
   ```

👉 This jumps execution to `_decode`

4. Run again

---

## 🎉 Step 8: Get the flag

Output:

```
value of DECODE picoCTF{M4cTim35!}
```

---

# 🏁 Final Flag

```
picoCTF{M4cTim35!}
```

---

# 🧠 Final Understanding (1 line)

👉 The program hides the flag inside **file timestamps (MAC times)** and we force it to run the hidden decode function using a debugger.

---

If you want, I can also give you:

* 🔓 Python script to extract flag without debugger
* 🧠 Diagram of how bytes are stored in timestamps

Just tell me 👍
