
Another interesting challenge.

The first thing I did was **unpack the binary**, since it was packed with UPX. I used:

```
upx -d unpackme-upx
```

After unpacking, I opened the binary in **Ghidra** to analyze the code.


### 🔍 Analysis

In the `main` function, I found a condition where the program asks:

```
What's my favorite number?
```

Then it checks if the input matches a specific value:

```c
if (local_44 == 0xb83cb)
```

So I converted this hexadecimal value to decimal:

```
0xb83cb = 754635
```

I ran the binary and entered the correct number:

```
./unpackme-upx
What's my favorite number? 754635
```

The program then decrypted and printed the flag:

```
picoCTF{up><_m3_f7w_5769b54e}


If you want, I can also make this more “pro CTF write-up style” or GitHub-ready 🔥
