So, in this Challenge, I noticed that directly calling the `win()` function no longer worked and returned **"Illegal input"**. This suggested that some kind of input filtering was in place.

Looking at the source code, the main logic is inside this loop:

```python
while(True):
  try:
    user_input = input('==> ')
    if( filter(user_input) ):
      eval(user_input + '()')
    else:
      print('Illegal input')
  except Exception as e:
    print(e)
    break
```

* The program continuously takes user input.
* It passes the input through a `filter()` function.
* If the input passes the filter, it executes:

  ```python
  eval(user_input + '()')
  ```

  This means whatever the user types is treated as a function name and executed.

For example:

* Input: `getRandomNumber`
* Executed as: `getRandomNumber()`

This explains why `getRandomNumber` works.

However, when trying:

* `win` → it gets blocked by the filter → **"Illegal input"**

The key issue here is the use of `eval()` on user input.

Even though the developer tries to restrict input to function names, `eval()` can execute **any valid Python expression**, not just function calls.


Instead of calling `win()`, I bypassed the restriction by directly executing Python code:

```bash
print(open('flag.txt', 'r').read())
```

This works because:

* `print(...)` is a valid Python expression
* It does not rely on calling restricted functions like `win`
* It directly reads the `flag.txt` file

<img width="1268" height="344" alt="image" src="https://github.com/user-attachments/assets/9bfc1638-c8f7-457f-b334-037eda2423bc" />

