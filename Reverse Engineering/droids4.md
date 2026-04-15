

In this challenge, I opened the APK using JADX and checked the `getFlag()` function.

The code creates four strings: `ace`, `jack`, `queen`, and `king`, all starting with `"aaa"`. Then it modifies each character using simple additions.

Instead of calculating manually, I copied the logic into a small Java program and ran it. This gave the password:

```id="g1p3zs"
alphabetsoup
```

### Step 1: Get the password

Run the app and enter:

```id="t7xk2m"
alphabetsoup
```

But it only returns `"call it"` instead of the flag.

### Step 2: Modify the app

So the app is not directly returning the flag. It returns a dummy string.

Looking at the code, there is another function:

```java id="n6q9yf"
public static native String cardamom(String str);
```

This likely returns the real flag.

### Step 3: Decompile APK

```bash id="w4f9j2"
apktool d four.apk --no-res
```

### Step 4: Edit smali code

Go to:

```id="k9z2ad"
smali/com/hellocmu/picoctf/FlagstaffHill.smali
```

Replace this:

```smali id="k3p1az"
const-string v5, "call it"
return-object v5
```

With this:

```smali id="p8x2ld"
invoke-static {p0}, Lcom/hellocmu/picoctf/FlagstaffHill;->cardamom(Ljava/lang/String;)Ljava/lang/String;
move-result-object v0
return-object v0
```

This makes the app call the real function that returns the flag.

### Step 5: Rebuild APK

```bash id="z1c8ys"
apktool b four -o recompiled_four.apk
```

### Step 6: Sign APK

```bash id="c2x7qn"
java -jar uber-apk-signer.jar --apks recompiled_four.apk
```

### Step 7: Run app

* Install modified APK
* Enter password: `alphabetsoup`
* Click button

Now the app shows the flag.

### Final Flag

picoCTF{not.particularly.silly}
