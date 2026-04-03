## How I Found the Flag

**1. Noticed hidden characters.** The file looked blank, but running `cat -A` on it revealed it was full of non-printable Unicode characters — not actually empty.

**2. Identified the two characters.** Using Python to count unique characters, the file contained only two: a regular space (`U+0020`) and an **em space** (`U+2003`). Two distinct characters = binary encoding.

**3. Decoded the binary.** Mapped em space → `0` and regular space → `1` (tried both ways), then grouped every 8 bits into a byte and converted to ASCII.

**4. Got the flag:**
```
picoCTF{not_all_spaces_are_created_equal_f5d46aff52c6e17f9fd6317b33d2d783}
```

The trick: the file uses **Unicode steganography** — hiding data in visually identical-looking whitespace characters. To the naked eye (and most text editors), the file looks completely blank. But two *different* space characters were used to encode binary data, which decoded straight to the flag.
