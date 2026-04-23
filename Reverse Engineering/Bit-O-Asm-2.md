## Bit-O-Asm-2 — PicoCTF Walkthrough (Reverse Engineering)

Same idea as Part 1 — ignore the noise, track what ends up in **EAX**.

The function again stores the inputs (`edi`, `rsi`) on the stack, but they are never used. The important instruction is:

```asm id="z9n8xk"
mov DWORD PTR [rbp-0x4], 0x9fe1a
```

This puts a constant value into a local variable. Immediately after, it gets moved into `EAX`:

```asm id="b2qk7m"
mov eax, DWORD PTR [rbp-0x4]
```

Since `EAX` holds the return value, the function simply returns that constant.

Now convert it:

```text id="0v0s8c"
0x9fe1a = 654874
```

---

## Final Answer

```text id="6c1c1g"
654874
```
