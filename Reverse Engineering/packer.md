First, I used the `file` command to identify the type of file. It turned out to be an ELF 64-bit binary. Since it is an executable, I gave it permission using `chmod +x` and ran it. When executed, the program asked for a password.

Next, I used the `strings` command to inspect the binary. Towards the end of the output, I noticed the presence of “UPX”, which indicates that the binary is packed using UPX (a tool used for compressing executables).

Since the binary was packed, I used the `upx -d` command to decompress it.

After unpacking, I opened the binary in Ghidra and analyzed the `main` function. I noticed several hex strings in the decompiled output. On closer inspection, I found the following condition:

<img width="1416" height="743" alt="image" src="https://github.com/user-attachments/assets/b3e78d13-bc4f-4120-b185-bc7fdf02ff20" />

```
if (iVar3 == 0) {
    puts("Password correct, please see flag: 7069636f4354467b5539585f556e5034636b314e365f42316e34526933535f35646565343434317d");
}
```

The program prints a hex-encoded string when the correct password is entered. To decode this hex string, I used the following command:

```
echo "7069636f4354467b5539585f556e5034636b314e365f42316e34526933535f35646565343434317d" | xxd -r -p
```
