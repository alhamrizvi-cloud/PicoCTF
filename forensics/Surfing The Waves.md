### Surfing the Waves — Writeup (Simple & Clear)

We are given a file: `main.wav`

At first it looks like just audio, but the hint says:

> “Music is cool, but what other kinds of waves are there?”
> “Look deep below the surface”

This means:

> Don’t listen to the audio — **analyze the raw data inside it**

### Step 1 — Observe the WAV Data

We read the file using Python:

```python
from scipy.io.wavfile import read
from numpy import unique

rate, data = read("main.wav")

print(data[:100])
print(unique(data))
```

### Step 2 — Notice the Pattern

From output:

```text
1000, 1500, 2000, 2500, ..., 8500
```

Each value looks like:

```text
(value = base * 500 + small noise)
```

Examples:

* 1000 → 2 * 500
* 1500 → 3 * 500
* 8500 → 17 * 500

So we normalize:

```python
i = value // 500 - 2
```

This gives numbers from **0 to 15**

👉 That’s exactly **hex (0–f)**

### Step 3 — Convert to Hex String

We map values to hex digits:

```python
from string import hexdigits

hex_str = ""

for val in data:
    i = val // 500 - 2
    hex_str += hexdigits[i]
```

Now we have a long hex string.

### Step 4 — Convert Hex to Text

```python
decoded = bytearray.fromhex(hex_str).decode()
print(decoded)
```

### Step 5 — What Do We Get?

Instead of plain text, we get **another Python script**.

That script shows how the WAV was generated.

Important part:

```python
hex_stuff = list(hexlify(content).decode("utf-8"))
```

👉 It converted a file into hex
👉 Then encoded it into sound

So what we decoded is actually:

> The original script used to generate the WAV

### Step 6 — Find the Flag

Scroll to the bottom of decoded output:

```python
# picoCTF{<REDACTED>}
```

👉 That’s the flag

If you want, I can show a **one-liner script** to solve this instantly or help you visualize it better.
