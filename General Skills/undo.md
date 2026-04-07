
## Step-by-Step Solution

### Step 1 – Base64 Encoding

**Hint:** Base64 encoded the string

The input was Base64 encoded. Normally, you would decode using:

```bash
base64 -d
```

However, the challenge restricted arguments and pipes. The only accepted command was:

```bash
base64 -d
```

---

### Step 2 – Reversed Text

**Hint:** Reversed the text

The string was reversed. To fix this:

```bash
rev
```

---

### Step 3 – Underscores Replaced with Dashes

**Hint:** Replaced underscores with dashes

We needed to convert `-` back to `_`:

```bash
tr '-' '_'
```

---

### Step 4 – Curly Braces Replaced with Parentheses

**Hint:** Replaced curly braces with parentheses

We reversed the transformation by converting parentheses back to braces:

```bash
tr '()' '{}'
```

---

### Step 5 – ROT13 Encoding

**Hint:** Applied ROT13 to letters

ROT13 shifts each letter by 13 positions. Since `rot13` command was blocked, we used `tr`:

```bash
tr 'a-zA-Z' 'n-za-mN-ZA-M'
```

---

## Final Flag

```
picoCTF{Revers1ng_t3xt_Tr4nsf0rm@t10ns_0ea42cd0}
```

