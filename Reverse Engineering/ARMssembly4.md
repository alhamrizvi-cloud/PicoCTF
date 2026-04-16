
## Idea

We are given ARM assembly functions.
We just need to **follow function calls step-by-step** and find the final number.

---

## Step 1: Start from main

```id="x1"
main → func1(1854822502)
```

---

## Step 2: func1

* Checks: is number ≤ 100? → **No**
* So it adds **100** and calls next function

```id="x2"
func2(1854822502 + 100)
= func2(1854822602)
```

---

## Step 3: func2

* Checks: is number > 499? → **Yes**
* So it adds **13** and calls next

```id="x3"
func5(1854822602 + 13)
= func5(1854822615)
```

---

## Step 4: func5 → func8

```id="x4"
func8(1854822615)
```

---

## Step 5: func8

* Simply adds **2**

```id="x5"
1854822615 + 2 = 1854822617
```

---

## Step 6: Final result

This value goes back to main:

```id="x6"
Final answer = 1854822617
```

---

## Step 7: Convert to hex

```id="x7"
1854822617 = 0x6e8e58d9
```

---

## Flag

```id="x8"
picoCTF{6e8e58d9}
```
