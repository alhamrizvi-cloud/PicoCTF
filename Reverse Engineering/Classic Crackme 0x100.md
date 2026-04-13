I opened the binary `crackme100` in Ghidra and decompiled the `main` function. Here's what I found at a high level.

Right at the top, the binary has a **hardcoded target string** baked in:
```
ztqittwtxtieyfrslgtzuxovlfdnbrsnlrvyhhsdxxrfoxnjbl
```
At the end, your input is compared against this string. If they match → you win.

Your input doesn't get compared *directly*. It gets **scrambled first**, then compared. The scrambling loop runs **3 times**, and on each pass, every character gets shifted like this:

```
new_char = (old_char - 'a' + popcount(position)) % 26 + 'a'
```

**What is popcount?** It's the number of `1` bits in the binary representation of the character's position index. For example:
- Position 0 = `0b00000000` → popcount = 0
- Position 3 = `0b00000011` → popcount = 2
- Position 7 = `0b00000111` → popcount = 3

The decompiled code looked like confusing bit-math, but it's a classic **SWAR popcount trick** — just a fancy way to count bits without a loop.

So essentially, each character gets **rotated forward in the alphabet** by a position-dependent amount, 3 times total.

## Step 3: Reversing It

To find the original password, I just ran the transformation **backwards**:
- Instead of **adding** the popcount shift, I **subtracted** it
- Done 3 times (once per original forward pass)

```python
target = "ztqittwtxtieyfrslgtzuxovlfdnbrsnlrvyhhsdxxrfoxnjbl"

def popcount(n):
    return bin(n).count('1')

result = list(target)
for _ in range(3):
    for i in range(len(result)):
        shift = popcount(i % 255)
        c = ord(result[i]) - ord('a')
        c = (c - shift) % 26
        result[i] = chr(c + ord('a'))

print(''.join(result))
```

Running the script gives:
```
zqncqnqkuncvswigianqoofjfwubsfgyilppbyjrroitflbuvc
```

Plugging that in:
```
Enter the secret password: zqncqnqkuncvswigianqoofjfwubsfgyilppbyjrroitflbuvc
SUCCESS! Here is your flag: picoCTF{...................................}
```

## Key Takeaways

| Concept | What it meant here |
|---|---|
| Hardcoded target | The scrambled password was sitting in the binary |
| SWAR popcount | Fancy bit trick = just count the 1-bits in the index |
| Caesar-style cipher | Each char shifted by a position-dependent amount |
| Reversing | Subtract instead of add, same number of rounds |
