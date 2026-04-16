

We are given:

* `rev_PS.ps` (PowerShell script)
* `output.txt`

Goal: find the correct `input.txt` → get flag

---

## Step 1: Understand basic checks

Script checks:

* File must be **9245 bytes**
* Max **5 lines**
* Only allowed characters:

  * `0`, `1`, space, newline

 So input is basically **binary data (0/1)**

---

## Step 2: Structure of input

Each line:

* Split by spaces
* Each block = **6 characters** (like `101010`)
* Total columns = **264**

Example:

```id="a1"
101010 111000 000111 ...
```

---

## Step 3: Column logic (important)

Script does something tricky:

* Instead of reading rows → it builds **columns**
* Each column becomes:

```id="a2"
[column0] = [block_from_line1, block_from_line2, ...]
```

---

## Step 4: Seeds + Random

Script generates:

* `seeds` → based on index
* `randoms` → using a formula

Then for each column:

```id="a3"
result = fun ^ previous_result ^ random
```

 Means:

* Everything depends on previous value
* One mistake → everything breaks

---

## Step 5: Hard part (skipped brute force)

We reverse logic using Python script:

* Recreate same operations
* Try to recover correct input

---

## Step 6: Final observation

After solving:

* Each column only had **2 possible patterns**
* That means → it's basically **binary (0 and 1)**

So we convert patterns → `0` and `1`

---

## Step 7: Decode binary

Binary → text → flag

---

## Flag

```id="a4"
picoCTF{2018highw@y_2_pow3r$hel!}
``
