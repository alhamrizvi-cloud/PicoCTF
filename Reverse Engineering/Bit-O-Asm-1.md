
## Bit-O-Asm-1 — PicoCTF Walkthrough (Reverse Engineering)

We are given a small assembly function. Instead of overcomplicating with full decompilation, we just track what goes into **EAX**, because in x86 that’s the return value.

The function stores the input arguments (`edi`, `rsi`) on the stack, but never actually uses them. That’s a common trick in these challenges — fake complexity.

The only instruction that matters is:

```asm
mov eax, 0x30
```

This directly sets the return value.

Since:

* `0x30` (hex) = `48` (decimal)

The function ignores everything and always returns **48**.

---

## Final Answer

```text
48
```

