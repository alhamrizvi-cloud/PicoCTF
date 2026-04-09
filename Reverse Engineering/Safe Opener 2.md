## Step 1: Download the decompiler

You downloaded the CFR Java decompiler using:

```bash
wget https://www.benf.org/other/cfr/cfr-0.152.jar
```

## Step 2: Decompile the `.class` file

```bash
java -jar cfr-0.152.jar SafeOpener.class > SafeOpener.java
```

### What this does:

* `java -jar` → runs the `.jar` file
* `cfr-0.152.jar` → the decompiler
* `SafeOpener.class` → input file (compiled Java bytecode)
* `>` → redirects output into a file
* `SafeOpener.java` → readable source code output

So basically:
👉 You converted compiled bytecode → back into readable Java code

## Step 3: View the decompiled code

```bash
cat SafeOpener.java
```

## Step 4: Analyze the code

Key parts of the code:

```java
encodedkey = encoder.encodeToString(key.getBytes());
```

👉 Whatever user enters is **Base64 encoded**

```java
String encodedkey = "picoCTF{SAf3_0p3n3rr_y0u_solv3d_it_198203f7}";
```

👉 This is the **target value**

```java
if (password.equals(encodedkey))
```

👉 The program checks:

* Your **Base64 encoded input**
* Against this **hardcoded string**

## Step 5: Understand the logic

Flow:

1. You enter a password
2. Program converts it → Base64
3. Compares it with:

   ```
   picoCTF{SAf3_0p3n3rr_y0u_solv3d_it_198203f7}
   ```
4. If equal → safe opens


ays return true (advanced RE)
