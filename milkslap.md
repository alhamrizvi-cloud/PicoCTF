
### Solution

First, I opened the website, the **.png file looked like a GIF animation**. It showed a cursor, and when you moved it, it slapped milk on the person. I spent around **6 minutes interacting with it**, thinking it might be part of the challenge.

After that, I checked the **source code** of the page to see if there was anything interesting. While looking through it, I discovered that there was an **image file that could be downloaded directly**.

I downloaded the image using:

```bash
wget http://wily-courier.picoctf.net:51386/concat_v.png
```

After downloading the file, I started analyzing it.

First, I used **binwalk** to check if there were any embedded files inside the image, but it didn’t find anything useful.

Then I used **exiftool** to check the metadata, but again nothing interesting appeared.

Next, I tried **steghide**, thinking the image might contain hidden data with a password, but it also returned nothing.

Finally, I ran **zsteg** to check for LSB steganography inside the PNG file.

```bash
zsteg concat_v.png
```

Surprisingly, the **flag appeared on the second line of the output**.

So the hidden data was stored using **LSB steganography in the PNG image**, which `zsteg` was able to detect immediately.
