
First, I used the **`file`** command to check what type of file it was, but it only showed **data**, which means the file header was probably corrupted.

So I opened the file in a hex editor and checked the first few bytes. I noticed that the **PNG header was corrupted**.

A valid PNG file must start with this header:

```id="yfjru0"
89 50 4E 47 0D 0A 1A 0A
```

But the file had incorrect bytes, so I manually fixed the header.

However, after fixing the header the image still did not work, which meant there were **more corrupted bytes in the file**.

Since I had solved many **picoCTF** challenges before, I already knew how to fix the PNG header because this trick appears in many challenges. But I didn’t know how to find the other corrupted parts.

So I sent the hex data to **Claude**, which helped identify the other corrupted bytes and repair the image.

These were the corrupted bytes that needed fixing:

| Offset    | Was         | Should Be   | Description          |
| --------- | ----------- | ----------- | -------------------- |
| 0x01      | 65          | 50          | PNG magic byte (`P`) |
| 0x03      | 34          | 47          | PNG magic byte (`G`) |
| 0x06      | B0          | 1A          | PNG magic byte       |
| 0x07      | AA          | 0A          | PNG magic byte       |
| 0x0C      | 43          | 49          | IHDR chunk type      |
| 0x0D      | 22          | 48          | IHDR chunk type      |
| 0x53      | AA          | 00          | IDAT chunk length    |
| 0x57–0x5A | 54 44 45 54 | 49 44 41 54 | IDAT chunk type      |

Another issue was that the **zlib Adler-32 checksum inside the IDAT stream was corrupted**, so it required bypassing the checksum during decompression.

After fixing all these corrupted bytes, the **PNG image decoded successfully**, and I was able to view the recovered image and obtain the flag.

thanks to claude!
  https://claude.ai/share/d317cd3b-5d7b-4a51-b2db-945474df39ad
