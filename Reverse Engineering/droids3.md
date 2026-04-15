
In this challenge, the app always shows “don’t wanna” when the button is clicked. The hint says “make this app your own”, which suggests we need to modify the app.

### Step 1: Analyze the code

I opened the APK in JADX and checked the `FlagstaffHill` class.

I found that:

* `getFlag()` calls a method named `nope()`
* `nope()` always returns “don’t wanna”
* There is another method called `yep()` which likely contains the real flag

So the goal is:
→ change the app so `getFlag()` calls `yep()` instead of `nope()`


### Step 2: Decompile APK

Using Apktool:

```bash
apktool d -r three.apk
```

This creates a folder with the app’s smali code.

### Step 3: Modify smali code

Go to:

```
smali/com/hellocmu/picoctf/FlagstaffHill.smali
```

Inside the `getFlag()` method:

* Find where `nope()` is called
* Replace it with `yep()`

This forces the app to return the real flag.

### Step 4: Rebuild APK

```bash
apktool b three -o three_modified.apk
```

### Step 5: Fix install error

The APK won’t install because it is not signed.

Error:

```
INSTALL_PARSE_FAILED_NO_CERTIFICATES
```

### Step 6: Create keystore

```bash
keytool -genkey -v -keystore pico.keystore -keyalg RSA -keysize 2048 -validity 10000 -alias pico
```

### Step 7: Align APK

```bash
zipalign -p 4 three_modified.apk three_aligned.apk
```

### Step 8: Sign APK

```bash
apksigner sign --ks-key-alias pico --ks pico.keystore three_aligned.apk
```

Verify:

```bash
apksigner verify three_aligned.apk
```

### Step 9: Run app

* Install the modified APK on emulator
* Enter anything in input
* Click button

Now it shows the flag.

### Final Flag

picoCTF{tis.but.a.scratch}

