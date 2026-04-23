Detailed writeup: https://medium.com/@alhamrizvi.in/armssembly-picoctf-walkthrough-reverse-engineering-by-alham-rizvi-e642402b0972

## ARMssembly — PicoCTF Walkthrough (Reverse Engineering)

We’re given an ARM binary and asked to find the input that makes the program print **“You win!”**. Instead of guessing, we reverse the logic.

After analyzing the assembly (or converting it to C), the core function looks like this:

```c
int func(int input) {
    int a = 88;
    int b = 4;
    int c = 3;

    int result = (a << b);
    result = result / c;
    result = result - input;

    return result;
}
```

The `main()` function simply does:

```c
int val = atoi(argv[1]);
int res = func(val);

if (res == 0) {
    puts("You win!");
} else {
    puts("You Lose :(");
}
```

---

## Understanding the Logic

The program computes a value internally, then subtracts your input from it. To win, the result must be zero.

So the condition becomes:

```text
(a << b) / c - input = 0
```

Rearranging:

```text
input = (a << b) / c
```

---

## Solve It

Now compute step by step:

```text
a = 88
b = 4
c = 3

a << b = 88 << 4 = 88 × 16 = 1408
1408 / 3 = 469   (integer division)
```

So the correct input is:

```text
input = 469
```

---

## Exploit

Run the binary with:

```bash
./chall 469
```

It prints:

```text
You win!
```

---

## Final Flag

```text
picoCTF{000000e5}
```
