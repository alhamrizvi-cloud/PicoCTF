
This challenge involved analyzing an Android APK file.

At first, I tried using **JADX** to decompile the APK and understand the code. However, this approach took a lot of time and didn’t immediately lead to the flag

Instead of fully reversing the APK, I switched to a quicker method:

* Unzipped the APK file (since APKs are just ZIP archives)
* Located the `classes3.dex` file
* Used the `strings` command to search for readable text

```
strings -t x classes3.dex | grep picoCTF
```

This directly revealed the flag:

```
picoCTF{t1m3r_r3v3rs3d_succ355fully_17496}
```

---

If you want, I can also give a clean commit message for this 🔥
