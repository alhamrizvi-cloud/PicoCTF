

## 1. What the program is doing

This line is the key:

```python
if user_input == "\x65"*1751:
```

Break it down:

### `\x65`

* This is a hexadecimal ASCII value.
* `0x65` = decimal **101**
* ASCII 101 = character **'e'**

So:

```python
"\x65" == "e"
```

---

## 2. What `"\x65"*1751` means

It means:

```
"e" repeated 1751 times
```

So the program expects:

```
eeeeeeeeeeeeeeeeeeeeeeeeeeee... (1751 times)
```

with:

* no spaces
* no newline
* exactly 1751 characters

---

## 3. Why your command worked

You used:

```bash
printf 'e%.0s' {1..1751} | nc foggy-cliff.picoctf.net 61606
```

This generates exactly:

* `e` printed 1751 times
* sent directly to the server

So it matched the condition and returned the flag.

---

## 4. Why the flag appeared

Because this condition became true:

```python
user_input == "eeeeee....(1751 times)"
```

So the program executed:

```python
print(open("./flag.txt", "r").read())
```

---

## 5. Core lesson

This challenge is testing:

* ASCII conversion (decimal → character)
* Exact string matching
* Repetition logic in Python
* Precision in payload formatting for CTFs
