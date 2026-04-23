## Bit-O-Asm-3 — PicoCTF Walkthrough (Reverse Engineering)

Now the function actually does a small calculation. Still, the approach is the same: follow what happens to **EAX**.

The inputs (`edi`, `rsi`) are stored but never used again. The real logic starts with two constants:

```asm id="r1"
mov DWORD PTR [rbp-0xc], 0x9fe1a
mov DWORD PTR [rbp-0x8], 0x4
```

So:

* `var_c = 0x9fe1a` → 654,874
* `var_8 = 4`

Next, the value is loaded into `EAX` and multiplied:

```asm id="r2"
mov eax, DWORD PTR [rbp-0xc]
imul eax, DWORD PTR [rbp-0x8]
```

This gives:

```text id="r3"
654,874 × 4 = 2,619,496
```

Then a constant is added:

```asm id="r4"
add eax, 0x1f5
```

Since:

```text id="r5"
0x1f5 = 501
```

Final calculation:

```text id="r6"
2,619,496 + 501 = 2,619,997
```

That value is returned via `EAX`.

---

## Final Answer

```text id="r7"
2619997
```
