

First, I opened the APK file using JADX to reverse the code.

In the main activity, I saw that the app has:

* 2 TextViews
* 1 EditText
* 1 Button

When the button is clicked, it shows “Not Today..”. So clearly, the flag is not directly displayed on the screen.

Looking at the code, the button calls a function named `getFlag()` from another class called `FlagstaffHill`.

In the `FlagstaffHill` class, the function always returns “Not Today…”, but there is something interesting. It logs a value using a tag named `PICO`.

This matches the hint:
“where else can output go? [PICO]”

This suggests that the output is being sent to Logcat instead of the app screen.

So instead of interacting with the UI, I checked the logs.


### Steps

1. Connect to the device:

```bash
adb shell
```

2. View logs:

```bash
logcat
```

3. Click the button multiple times to generate logs.

4. Search for logs with tag `PICO`.


In Logcat, the flag appears in the logs.

### Final Flag

picoCTF{a.moose.once.bit.my.sister}
