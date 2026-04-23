## Bit-O-Asm-4 — PicoCTF Walkthrough (Reverse Engineering)

Same strategy: ignore unused inputs, follow the value that ends up in **EAX**.

The function initializes a variable:

```asm id="b4a"
mov DWORD PTR [rbp-0x4], 0x9fe1a
```

So:

```text id="b4b"
var = 0x9fe1a = 654,874
```

Then it compares this value:

```asm id="b4c"
cmp DWORD PTR [rbp-0x4], 0x2710
```

Since:

```text id="b4d"
0x2710 = 10,000
```

Condition:

```text id="b4e"
654,874 <= 10,000 → false
```

So the `jle` (jump if less or equal) is **not taken**, and execution goes to the `else` path:

```asm id="b4f"
sub DWORD PTR [rbp-0x4], 0x65
```

Since:

```text id="b4g"
0x65 = 101
```

Final calculation:

```text id="b4h"
654,874 - 101 = 654,773
```

That value is moved into `EAX` and returned.

---

## Final Answer

```text id="b4i"
654773
```
