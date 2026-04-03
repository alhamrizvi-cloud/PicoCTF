
This challenge is basically:
👉 “Understand weird syntax → figure out what inputs it wants → get output → convert to ASCII → flag”

---

## 🎸 Step 1: Weird Rockstar = Normal Variables

Rockstar uses **poetry to store numbers**:

* Each word length = a digit
* Example:

  ```
  a six-string
  ```

  → "a" (1) + "six" (3) + "string" (6) = **136**

So:

* `A guitar is a six-string` → guitar = **136**
* `Tommy's been down` → "been" (4) → tommy = **4**
* `Music is a billboard-burning razzmatazz!`
  → word lengths = **1970**

---

## 🎧 Step 2: First Input

```
Listen to the music
If the music is a guitar
Say "Keep on rocking!"
```

Means:

```cpp
input1 = ?
if (input1 == 136)
    print "Keep on rocking!"
```

👉 So first input = **136**

---

## 🥁 Step 3: Second Input

```
Listen to the rhythm
If the rhythm without Music is nothing
```

Translate:

* "without" = subtraction
* "nothing" = 0

So:

```cpp
input2 - music == 0
```

We already know:
👉 music = **1970**

So:

```cpp
input2 - 1970 = 0
→ input2 = 1970
```

👉 Second input = **1970**

---

## 🔥 Step 4: What Happens Next?

After correct inputs:

* Program prints some numbers / encoded output
* That output = ASCII values

Convert those numbers → letters

👉 You get:

```
BONJOVI
```

---

## 🏁 Final Flag

```
picoCTF{BONJOVI}
```

---

## ⚡ TL;DR

* Rockstar = word lengths = numbers
* First input → **136**
* Second input → **1970**
* Output → ASCII decode → **BONJOVI**
