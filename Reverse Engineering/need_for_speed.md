
## Step 1: Analyze the binary

In GDB:

```bash
gdb ./need-for-speed
```

List functions:

```gdb
info functions
```

Important functions found:

```text
get_key
set_timer
alarm_handler
print_flag
main
```

* `set_timer()` + `alarm_handler()` → cause BOOM
* `print_flag()` → prints flag
* `get_key()` → generates key

##  Problem

When running normally:

```text
Keep this thing over 50 mph!
Creating key...
Not fast enough. BOOM!
```

Program uses a **timer (alarm)**
If you’re “slow” → BOOM

---

## Idea

Instead of solving timing logic:

> Just directly call the function that prints the flag

---

## Step 2: Exploit using GDB

### 1. Set breakpoint at main

```gdb
b main
```

---

### 2. Run program

```gdb
run
```

Stops at:

```text
Breakpoint at main
```

---

### 3. Call key function (optional but clean)

```gdb
call (int) get_key()
```

Output:

```text
Creating key...
Finished
```

---

### 4. Call flag function

```gdb
call (int) print_flag()
```

---

## Output

```text
Printing flag:
PICOCTF{Good job keeping bus #33feb8f9 speeding along!}
```

---

## 🏁 Final Flag

```text
PICOCTF{Good job keeping bus #33feb8f9 speeding along!}
```


