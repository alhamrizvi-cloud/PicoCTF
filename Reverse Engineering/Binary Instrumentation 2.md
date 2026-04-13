### Short Write-up (Part 2)

Used `binwalk` to scan the executable and found embedded LZMA compressed data. Extracted it using `binwalk -e`, which produced a file (`6000`).

Ran `strings` on the extracted file and found a Base64-encoded flag hidden in a message. Decoded it using `base64 -d` to get the final flag:


picoCTF{fr1da_f0r_b1n_in5trum3nt4tion!_b21aef39}
