# asm4 Writeup

## The Problem
Find what `asm4("picoCTF_95473")` returns.

---

## Step 1: Identify Variables

First, map out what each local variable means:

```
[ebp-0x10] = accumulator    → starts at 0x277
[ebp-0xc]  = len            → string length
[ebp-0x8]  = i              → loop counter
[ebp+0x8]  = str            → pointer to "picoCTF_95473"
```

---

## Step 2: The String (as bytes)

The hint says **treat the array as a pointer** — so we index into it byte by byte:

```
Index:  0    1    2    3    4    5    6    7    8    9   10   11   12   13
Char:   p    i    c    o    C    T    F    _    9    5    4    7    3   \0
Hex:   70   69   63   6f   43   54   46   5f   39   35   34   37   33   00
```

---

## Step 3: Phase 1 — Find String Length

```asm
mov  DWORD PTR [ebp-0xc], 0x0     ; len = 0
jmp  +31                           ; jump into loop check

+27: add  DWORD PTR [ebp-0xc], 0x1 ; len++
+31: mov  edx, [ebp-0xc]           ; edx = len
     mov  eax, [ebp+0x8]           ; eax = str base
     add  eax, edx                 ; eax = &str[len]
     movzx eax, BYTE PTR [eax]     ; eax = str[len]
     test al, al                   ; is it 0 (null)?
     jne  +27                      ; if not null, loop
```

This is simply **strlen** — count until `\0`:

```
"picoCTF_95473" → len = 13
```

---

## Step 4: Phase 2 — Main Loop

```asm
mov  DWORD PTR [ebp-0x8], 0x1     ; i = 1
jmp  +142                          ; jump to loop condition
```

### Loop Condition (at +142):
```asm
mov  eax, [ebp-0xc]       ; eax = len (13)
sub  eax, 0x1             ; eax = 12
cmp  [ebp-0x8], eax       ; compare i with 12
jl   +55                  ; if i < 12, continue loop
```
So loop runs for **i = 1, 2, 3, ... 11**

---

### What Each Iteration Does

Breaking down the body into two parts:

**Part A** (lines +55 to +88):
```asm
edx = str[i]              ; current char
eax = str[i-1]            ; previous char
edx = edx - eax           ; diff1 = str[i] - str[i-1]
ebx = [ebp-0x10] + edx    ; ebx = acc + diff1
```

**Part B** (lines +98 to +135):
```asm
edx = str[i+1]            ; next char
eax = str[i]              ; current char
edx = edx - eax           ; diff2 = str[i+1] - str[i]
eax = edx + ebx           ; eax = acc + diff1 + diff2
[ebp-0x10] = eax          ; store back to acc
```

### Simplified Math:
```
acc += (str[i] - str[i-1]) + (str[i+1] - str[i])

     = str[i] - str[i-1] + str[i+1] - str[i]

     = str[i+1] - str[i-1]        ← middle term cancels!
```

> **Each iteration:** `acc += str[i+1] - str[i-1]`

---

## Step 5: Trace Every Iteration

Starting value: `acc = 0x277 = 631`

| i | str[i-1] | str[i+1] | diff (decimal) | acc |
|---|----------|----------|----------------|-----|
| 1 | 'p' = 112 | 'c' = 99  | 99-112 = **-13** | 631-13 = **618** |
| 2 | 'i' = 105 | 'o' = 111 | 111-105 = **+6**  | 618+6  = **624** |
| 3 | 'c' = 99  | 'C' = 67  | 67-99  = **-32** | 624-32 = **592** |
| 4 | 'o' = 111 | 'T' = 84  | 84-111 = **-27** | 592-27 = **565** |
| 5 | 'C' = 67  | 'F' = 70  | 70-67  = **+3**  | 565+3  = **568** |
| 6 | 'T' = 84  | '_' = 95  | 95-84  = **+11** | 568+11 = **579** |
| 7 | 'F' = 70  | '9' = 57  | 57-70  = **-13** | 579-13 = **566** |
| 8 | '_' = 95  | '5' = 53  | 53-95  = **-42** | 566-42 = **524** |
| 9 | '9' = 57  | '4' = 52  | 52-57  = **-5**  | 524-5  = **519** |
| 10 | '5' = 53 | '7' = 55  | 55-53  = **+2**  | 519+2  = **521** |
| 11 | '4' = 52 | '3' = 51  | 51-52  = **-1**  | 521-1  = **520** |

---

## Step 6: Return Value

```asm
mov eax, [ebp-0x10]    ; return acc
ret
```

```
520 decimal = 0x208 hex
```

---

## ✅ Flag: `0x208`

---

## Summary in One Line
> The function starts with `acc = 0x277`, then for each index `i` from 1 to len-2, adds `str[i+1] - str[i-1]` to acc. The middle terms always cancel, making it a simple sliding window difference across the string.
