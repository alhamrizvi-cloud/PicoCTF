
```bash
chmod +x gdbme
```

* Makes the file **executable**
* Without this, Linux won’t let you run `./gdbme`

---

### 2.

```bash
gdb gdbme
```

* Opens the binary inside **GNU Debugger (GDB)**
* You can now inspect, pause, and manipulate execution

---

### 3.

```gdb
layout asm
```

* Switches GDB to **assembly view (TUI mode)**
* Shows:

  * Disassembled instructions
  * Current instruction pointer (RIP)
* Useful for reverse engineering when no source code is available

---

### 4.

```gdb
break *(main+99)
```

* Sets a **breakpoint at an exact memory offset**
* Meaning:

  * Go to function `main`
  * Move **99 bytes forward**
  * Stop execution there

👉 Why?

* You already analyzed the binary and found:

  * At `main+99` → some check happens (like password validation / failure branch)

---

### 5.

```gdb
run
```

* Starts execution of the program
* It will **pause automatically** when it hits the breakpoint at `main+99`

---

### 6.

```gdb
jump *(main+104)
```

* This is the **important trick**
* It **forces execution to jump to another instruction**
* Skips instructions between `+99` and `+104`

👉 In CTF context:

* You’re likely skipping:

  * a comparison (`cmp`)
  * a conditional jump (`jne`, `je`, etc.)
* So instead of:

  ```
  if (wrong) → exit
  ```

  you force:

  ```
  go directly to "correct" path
  ```

