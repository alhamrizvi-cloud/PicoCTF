Detailed writeup on medium: https://medium.com/@alhamrizvi.in/virtual-machine-0-picoctf-writeup-reverse-engineering-by-alham-rizvi-dfb68087597e
## Virtual Machine 0 — PicoCTF Walkthrough (Reverse Engineering)

We are given a `.dae` file representing a mechanical system and a large numeric input. The goal is to understand how the system transforms the input and recover the flag.

Opening the `.dae` model in a viewer reveals a simple gear mechanism consisting of three gears: a red gear (input), a grey gear (intermediate), and a blue gear (output). By inspecting the model, we identify the number of teeth on the gears: the red gear has 40 teeth and the grey gear has 8 teeth.

From basic gear mechanics, the rotation ratio between two meshing gears is determined by the ratio of their teeth. A smaller gear spins faster. So the transformation is:

```
ratio = 40 / 8 = 5
```

This means for every single rotation of the input (red axle), the output (blue axle) rotates 5 times. So the entire system simplifies to:

```
f(x) = 5x
```

We are given a large input number, so we multiply it by 5 to get the output.

---

## Exploit Script

```python
# given input
x = 39722847074734820757600524178581224432297292490103995908738058203639164185

# apply transformation
result = x * 5

# convert to hex
hex_val = hex(result)[2:]

# convert to ASCII
flag = bytes.fromhex(hex_val).decode()

print(flag)
```

---

## Final Flag

```
picoCTF{g34r5_0f_m0r3_3537e50a}
```

---

## Summary

The challenge looks complex with a 3D model, but the logic reduces to a simple gear ratio. Once the ratio is identified, the rest is just number conversion.
