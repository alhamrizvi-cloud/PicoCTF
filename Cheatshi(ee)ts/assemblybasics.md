# Assembly Cheatsheet (x86-64 basics)

## 1. Function Setup & Cleanup

### `push rbp`

* Saves old base pointer on stack
* Like: “backup previous function state”

---

### `mov rbp, rsp`

* Sets new base pointer
* Marks start of current function’s stack frame

---

### `pop rbp`

* Restores old base pointer
* Undo `push rbp`

---

### `ret`

* Returns from function
* CPU jumps back to caller
* Return value is in **EAX**

---

## 2. Data Movement

### `mov dest, src`

* Copies data from `src` → `dest`
* Most common instruction

Example:

```id="ex1"
mov eax, 5
```

→ EAX = 5

---

### Memory access

```id="ex2"
mov DWORD PTR [rbp-0x4], edi
```

* Store value from `edi` into memory
* `[rbp-0x4]` = local variable

---

## 3. Registers You Saw

### `eax`

* Holds **return value**

---

### `edi`

* First argument to function

---

### `rsi`

* Second argument

---

## 4. Arithmetic Operations

### `add dest, value`

* Adds value

```id="ex3"
add eax, 5
```

→ eax = eax + 5

---

### `sub dest, value`

* Subtracts value

```id="ex4"
sub eax, 5
```

→ eax = eax - 5

---

### `imul dest, value`

* Multiply

```id="ex5"
imul eax, 4
```

→ eax = eax × 4

---

## 5. Comparison & Conditions

### `cmp a, b`

* Compares `a` and `b`
* Internally does: `a - b`
* Sets flags (no value stored)

---

### `jle` (Jump if Less or Equal)

* Jump if:

```id="cond1"
a <= b
```

---

### `jmp`

* Unconditional jump
* Always jumps

---

## 6. Control Flow (If-Else Logic)

Assembly version of:

```c id="ifc"
if (a <= b) {
    do_this;
} else {
    do_that;
}
```

Becomes:

```id="ifasm"
cmp a, b
jle do_this
do_that
jmp end
do_this:
...
end:
```

---

## 7. Constants (Hex vs Decimal)

* `0x30` = 48
* `0x65` = 101
* `0x2710` = 10,000
* `0x9fe1a` = 654,874

---

# Final Key Concepts You Learned

* Functions often:

  * Store arguments → but may not use them
* **EAX = return value**
* Many challenges just:

  * Assign constant
  * Do math
  * Return result
* Conditions use:

  * `cmp` + jump (`jle`, etc.)

---

# Super Short Summary

* `mov` → copy data
* `add/sub/imul` → math
* `cmp` → compare
* `jle/jmp` → control flow
* `eax` → final answer


