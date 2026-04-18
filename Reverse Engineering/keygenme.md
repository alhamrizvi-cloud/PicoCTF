This has to be one of the hardest challenges I’ve ever done in my life. It was also my first time using radare2, and honestly, I tried multiple decompilers just to understand what was going on.

I spent around 3 hours on this challenge. For the first couple of hours, I was mostly stuck, experimenting and trying different approaches. In the last hour, I finally gave in and read a writeup but even then, I still didn’t fully understand everything.

Overall, it was really tough, but I learned a lot from it. Definitely a challenging experience.

### 1. Goal
Find the correct license key for the `./keygen` binary using dynamic analysis.

### 2. Reconnaissance
Initial execution shows the program expects a specific string format:
```bash
$ ./keygen
Enter your license key: AAAA
That key is invalid.
```
Opening the file in `radare2` with full analysis (`-AA`) reveals a `main` function and a validation function `fcn.00001209`.

### 3. Analysis of the Validation Logic
By examining the assembly of the validation function (`pdf @ fcn.00001209`), we see the program:
* Initializes several strings like `picoCTF{`, `br1ng_y0`, and `ur_0wn_k`.
* Performs MD5 hashing operations.
* **Crucially:** It runs a loop that populates a buffer at `rbp - 0x30` with characters.
* After the loop, it performs a comparison:
    `0x561aad97c455  38c2  cmp dl, al`
    This compares the user-input character (`dl`) with the generated character (`al`).



### 4. The Attack (Dynamic Instrumentation)
Instead of manually calculating the MD5 hashes, we let the program run until the key is fully built in memory.

**Commands Used:**
1.  **Open in Debug Mode:** `r2 -d ./keygen`
2.  **Analyze:** `aa` (Analyze all).
3.  **Break at the check:** We set a breakpoint right before the comparison loop begins.
    `db 0x561aad97c40a`
4.  **Execute:** `dc` (Continue). The program asks for input; we provide a dummy key (e.g., `lol`).
5.  **Examine Memory:** Once the breakpoint is hit, the full key is sitting in a stack buffer. Based on the assembly, the buffer starts at `rbp - 0x30`.

### 5. Extraction
We use the `psz` (Print String Zero-terminated) command at the calculated memory address:
```bash
[0x561aad97c40a]> psz @ rbp-0x30
picoCTF{br1ng_y0ur_0wn_k3y_abb48a6c}
```
<img width="1366" height="693" alt="image" src="https://github.com/user-attachments/assets/9d530ded-7e24-4993-bd10-7cbcf450bd59" />

<img width="1366" height="693" alt="image" src="https://github.com/user-attachments/assets/4363f4b5-ae54-41ee-92f5-3e73ab00d52a" />

<img width="1366" height="693" alt="image" src="https://github.com/user-attachments/assets/b473c1f9-10c9-4648-ad9d-38dc61bf2d70" />

<img width="1366" height="693" alt="image" src="https://github.com/user-attachments/assets/88b717a6-8939-4878-8f4f-24a407f2ff00" />

---

### Command Cheat Sheet

| Command | Explanation |
| :--- | :--- |
| **`r2 -AA ./keygen`** | Opens the file and runs all analysis scripts immediately. |
| **`doo`** | Re-opens the file in **debug mode** (starts the process). |
| **`db <addr>`** | **Debug Breakpoint**: Stops execution when the CPU reaches this address. |
| **`dc`** | **Debug Continue**: Runs the program until a breakpoint or crash. |
| **`pdf`** | **Print Disassemble Function**: Shows the assembly code of the current function. |
| **`dr rbp`** | **Debug Register**: Shows the value inside the Base Pointer register (used to find the stack). |
| **`psz @ rbp-0x30`** | **Print String**: Reads memory at the address of RBP minus 48 bytes (0x30 hex). |

**Flag Found:** `picoCTF{br1ng_y0ur_0wn_k3y_abb48a6c}`

What's the next challenge on the list?
