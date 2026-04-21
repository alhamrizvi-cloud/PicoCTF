I wasted some time in ghidra, but didn't get any clues so,

I used `xxd` to inspect the raw bytes of the PNG files and looked at the end using `tail`.

While comparing `mystery.png`, `mystery2.png`, and `mystery3.png`, I noticed something interesting near the `IEND` chunk (which marks the end of a PNG file). After this marker, there were extra bytes that didn’t belong to a normal PNG.

In `mystery.png`, I saw:

```
CF{An1_68311242}
```

In `mystery3.png`, I found:

```
icT0tha_
```

It was clear that the bytes were **scrambled across multiple files**.

So I combined the pieces in the correct order:

```
picoCTF{An0tha_1_68311242}
```

---

* PNG files end at `IEND`
* Anything after that is extra (hidden data)
* The flag was split and scrambled across multiple images
* Reconstructing those parts gives the final flag

