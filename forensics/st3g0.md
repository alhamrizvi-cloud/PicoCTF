First, I tried Binwalk to check if there were any hidden or embedded files inside the image. After that, I used ExifTool to inspect the metadata, but it didn’t reveal anything useful.

Then I thought it might be hidden using Steghide, so I tried extracting data with it. However, it required a passphrase, which I didn’t have.

So I decided to try zsteg with the -a option to perform a full scan.

 "picoCTF{7h3r3_15_n0_5p00n_a1062667}$t3g0"
b1,abgr,lsb,xy      .. text: "E2A5q4E%uSA"
b2,b,lsb,xy         .. text: "AAPAAQTAAA"
b2,b,msb,xy         .. text: "HWUUUUUU"
b3p,r,lsb,xy        .. text: "Kooooooooohp"
b3p,b,lsb,xy        .. text: "!\" KKjONH4%"
b3p,b,msb,xy        .. text: ["R" repeated 10 time
