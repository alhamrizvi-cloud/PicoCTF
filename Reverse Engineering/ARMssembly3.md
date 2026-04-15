
## What the Program Does

The program takes a command-line argument, converts it to an integer via `atoi`, passes it to `func1`, and prints the result.

---

## Function-by-Function Breakdown

### `func2(w0)`
```asm
func2:
    sub  sp, sp, #16
    str  w0, [sp, 12]    ; store argument
    ldr  w0, [sp, 12]    ; load it back
    add  w0, w0, 3       ; add 3
    ret                  ; return
```
**Simply adds 3 to its input and returns it.**

---

### `func1(w0)`
```asm
func1:
    str  wzr, [x29, 44]   ; result = 0
    b    .L2              ; jump to condition check

.L4:                      ; loop body
    ldr  w0, [x29, 28]
    and  w0, w0, 1        ; isolate lowest bit
    cmp  w0, 0
    beq  .L3              ; if bit == 0, skip
    ldr  w0, [x29, 44]
    bl   func2            ; result = func2(result)  → result += 3
    str  w0, [x29, 44]

.L3:
    ldr  w0, [x29, 28]
    lsr  w0, w0, 1        ; n >>= 1  (right shift by 1)
    str  w0, [x29, 28]

.L2:
    ldr  w0, [x29, 28]
    cmp  w0, 0
    bne  .L4              ; loop while n != 0
```

**What it does:**
- Loops through every bit of the input
- Every time it finds a `1` bit, it adds 3 to the accumulator
- Result = `popcount(n) × 3`

---

### `main`
```asm
ldr  x0, [x29, 16]     ; load argv
add  x0, x0, 8         ; argv[1]  (skip argv[0])
ldr  x0, [x0]
bl   atoi              ; convert string → int
bl   func1             ; call func1
; print result
```

---

## Summary

| Function | Behaviour |
|---|---|
| `main` | Reads `argv[1]`, calls `atoi`, passes to `func1`, prints result |
| `func1` | Counts 1-bits in input, multiplies count by 3 |
| `func2` | Adds 3 to its argument |

---

## Python Solution

```python
def func2(x):
    return x + 3

def func1(n):
    n = n & 0xFFFFFFFF   # treat as 32-bit unsigned
    result = 0
    while n != 0:
        if n & 1:            # if lowest bit is 1
            result = func2(result)  # result += 3
        n >>= 1              # shift right
    return result & 0xFFFFFFFF

# Input argument
a = 4101707659

result = func1(a)

print(f"Result:     {result}")
print(f"Hex:        {hex(result)}")
print(f"Flag:       picoCTF{{{result:08x}}}")
```

**Output:**
```
Result:     57
Hex:        0x39
Flag:       picoCTF{00000039}
```

---

## Step-by-Step Trace

```
Input:   4101707659  =  0xF47B178B
Binary:  1111 0100 0111 1011 0001 0111 1000 1011
                                                 ↑ count the 1s

1-bits:  19
Result:  19 × 3 = 57 = 0x39
```

**Flag: `picoCTF{00000039}`**

---

## Key Concepts Used

- **ARM register conventions** — `w0` is the 32-bit view of `x0`, used for function arguments/return values
- **`lsr`** — Logical Shift Right, equivalent to `>> 1` in C
- **`and w0, w0, 1`** — isolates the LSB (least significant bit) to check if it's 1
- **`bl`** — Branch with Link, i.e. a function call
- **`popcount`** — counting set bits, the core operation here
