
The program takes an integer input, passes it to `func1`, and prints the result. The goal is to understand what `func1` does and compute the final output.

### Function Analysis

#### `func1`

From the assembly logic:

* The input value is stored as `arg`
* Two variables are initialized:

  * `sum = 0`
  * `i = 0`

Loop structure:

```
while (i < arg):
    sum += 3
    i += 1
```

This loop runs `arg` times and adds 3 each iteration.

So the final result is:

```
result = 3 × arg
```

### Given Input

```
arg = 2401941830
```

### Calculation

```
3 × 2401941830 = 7205825490
```

Since the result is stored in a 32-bit register (`w0`), it overflows:

```
7205825490 mod 2^32 = 2910858194
```

### Convert to Hex

```
2910858194 = 0xAD802BD2
```

### Final Flag

```
picoCTF{ad802bd2}
```


* The loop runs `arg` times
* Each iteration adds 3
* Result is stored in a 32-bit register, so overflow occurs
* Final answer is `(3 × arg) mod 2^32`
