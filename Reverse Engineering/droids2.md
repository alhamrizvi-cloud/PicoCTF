
In this challenge, the app looks similar to the previous ones, but the hint says “smali sounds like an ikea bookcase”, suggesting we may need to understand how the code works.

### Step 1: Analyze the code

I opened the APK in JADX and went to the `FlagstaffHill` class.

There is an array:

```java id="z3f6kx"
String[] witches = {"weatherwax", "ogg", "garlick", "nitt", "aching", "dismass"};
```

Then some variables are calculated:

```java id="k3a3pd"
int second = 3 - 3;        // 0
int third = (3 / 3) + second;  // 1
int fourth = (third + third) - second; // 2
int fifth = 3 + fourth;   // 5
int sixth = (fifth + second) - third; // 4
```

### Step 2: Build the password

The password is created by combining values from the array:

```java id="1h9t3p"
dismass.ogg.weatherwax.aching.nitt.garlick
```

### Step 3: Get the flag

1. Run the app
2. Enter the password:

```id="zq0sh9"
dismass.ogg.weatherwax.aching.nitt.garlick
```

3. Click the button

The app then returns the flag.

### Final Flag

picoCTF{what.is.your.favourite.colour}

