Here is your writeup without subtopics and extra headings:

---

We are given a binary that asks for a numeric code greater than 999. The goal is to find an input that satisfies all checks and reveals the flag.

After loading the binary into Ghidra and analyzing the `main` function, we observe the following logic:

```c
printf("Enter a numeric code (must be > 999 ): ");
__isoc99_scanf("%d", &raw_input);

valid = is_valid_decimal(raw_input);
if (valid == 0) {
    valid = is_valid_hex(raw_input);
    if (valid == 0) {
        puts("Invalid input.");
        return;
    }
    long_input = strtol(raw_input, (char **)0x0, 0x10);
    int_input = (int)long_input;
}
else {
    int_input = atoi(raw_input);
}

if (int_input < 1000) {
    puts("Too small.");
}
else if (int_input < 10000) {
    if (strlen(int_input) == 3) {
        reveal_flag();
    } else {
        puts("Access Denied.");
    }
}
else {
    puts("Too high.");
}
```

The program first checks whether the input is a valid decimal number. If not, it checks if it is valid hexadecimal and converts it using base 16. After conversion, the integer must be greater than 999 and less than 10000. Additionally, the length of the original input must be exactly 3 characters.

This creates a contradiction for decimal input, because any decimal number greater than 999 must have at least 4 digits. Therefore, decimal input cannot satisfy the length condition.

To bypass this, we use hexadecimal input. A 3-character hexadecimal value can represent numbers greater than 999 in decimal. For example, 1000 in decimal is 0x3E8 in hexadecimal. So valid inputs include values like `3e8`, `3e9`, or `3ff`.

Using `3e9` works and produces the following output:

```
Access granted:
}847ftc_oc_ipd936ftc_oc_ipb_99ftc_oc_ip9_TGftc_oc_ip_xehftc_oc_ip_tigftc_oc_ipid_3ftc_oc_ip{FTCftc_oc_ipocipftc_oc_ip
```

The output is obfuscated. It contains a repeated separator string `ftc_oc_ip` and is reversed. To recover the flag, we remove the separator and reverse the string.

```python
raw = "}847ftc_oc_ipd936ftc_oc_ipb_99ftc_oc_ip9_TGftc_oc_ip_xehftc_oc_ip_tigftc_oc_ipid_3ftc_oc_ip{FTCftc_oc_ipocipftc_oc_ip"

cleaned = raw.replace("ftc_oc_ip", "")
flag = cleaned[::-1]
print(flag)
```

This gives the final flag:

```
picoCTF{3_digit_hex_GT_999_b639d748}
```

The key idea is that decimal input fails the length constraint, but hexadecimal allows a 3-character input that satisfies all conditions, and the final output just needs simple decoding.
