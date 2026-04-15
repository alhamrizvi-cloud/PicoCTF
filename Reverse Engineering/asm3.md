# asm3 Writeup

## The Problem
Find what `asm3(0xccf13937, 0xbf0a24e4, 0xf44d6917)` returns.

---

## Key Concept: Stack Memory Layout

When arguments are pushed onto the stack in **little-endian x86**, they are stored **byte by byte, lowest byte first**:

```
arg1 = 0xccf13937 stored as: 37 39 f1 cc
arg2 = 0xbf0a24e4 stored as: e4 24 0a bf
arg3 = 0xf44d6917 stored as: 17 69 4d f4
```

So the stack looks like this:

```
Address  │ Byte
─────────┼──────
ebp+8    │ 0x37  ← arg1 starts
ebp+9    │ 0x39
ebp+a    │ 0xf1
ebp+b    │ 0xcc
ebp+c    │ 0xe4  ← arg2 starts
ebp+d    │ 0x24
ebp+e    │ 0x0a
ebp+f    │ 0xbf
ebp+10   │ 0x17  ← arg3 starts
ebp+11   │ 0x69
ebp+12   │ 0x4d
ebp+13   │ 0xf4
```

---

## Register Anatomy

`eax` is a 32-bit register. We only care about the lower 16 bits:
```
eax = [ high 16 bits | ah | al ]
                       ↑    ↑
                ax = [ ah  | al ]  ← 16-bit
```

---

## Tracing Each Instruction (with math)

### Step 1: `xor eax, eax`
```
ax = 0x0000
```
Anything XOR itself = 0. Clears the register.

---

### Step 2: `mov ah, [ebp+0x9]`
Fetch byte at `ebp+9` = **0x39**, put into `ah` (high byte of ax):
```
ax = 0x3900
```

---

### Step 3: `shl ax, 0x10`
Shift ax LEFT by 16 bits (0x10 = 16):
```
0x3900 << 16 = 0x39000000
```
But ax is only **16 bits wide**, so everything shifts out:
```
ax = 0x0000
```
> **Math:** Any 16-bit number × 2¹⁶ overflows a 16-bit register → result = 0

---

### Step 4: `sub al, [ebp+0xd]`
Fetch byte at `ebp+d` = **0x24**, subtract from `al` (low byte):
```
al = 0x00 - 0x24

In hex:  0x00 - 0x24 = 0xdc  (underflow wraps around in 8-bit)
In math: 0 - 36 = -36 ≡ 220 (mod 256) = 0xdc ✓
```
```
ax = 0x00dc
```

---

### Step 5: `add ah, [ebp+0xf]`
Fetch byte at `ebp+f` = **0xbf**, add to `ah` (high byte):
```
ah = 0x00 + 0xbf = 0xbf
```
```
ax = 0xbfdc
```

---

### Step 6: `xor ax, [ebp+0x12]`
Fetch **word** (2 bytes) at `ebp+0x12`:
```
[ebp+12] = 0x4d
[ebp+13] = 0xf4
little-endian word = 0xf44d
```
Now XOR:
```
  0xbfdc
⊕ 0xf44d
────────
= 0x4b91
```
**Bit by bit math:**
```
b = 1011 1111 1101 1100
f = 1111 0100 0100 1101
XOR─────────────────────
  = 0100 1011 1001 0001
  = 0x4b91 ✓
```

---

## Final Answer

```
0x4b91
```
