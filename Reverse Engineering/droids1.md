
I again opened the APK using JADX and looked at the `getFlag()` function.

The function checks if the user input matches a password stored in the app:

```java
if (input.equals(ctx.getString(R.string.password))) {
    return fenugreek(input);
}
```

So the goal is to find the value of `R.string.password`.

Inside the code, `R.string.password` points to a resource ID:

```
2131427375
```

To find its actual value, I checked the resources:

* Go to: `resources.arsc`
* Then: `res/values/strings.xml`

There, I found:

```xml
<string name="password">opossum</string>
```

So the password is:

```
opossum
```

1. Start an Android emulator (AVD)
2. Install the APK
3. Enter the password: `opossum`
4. Click the button

The app then displays the flag.

picoCTF{pining.for.the.fjords}


