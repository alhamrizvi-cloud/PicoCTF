
yo chads, welcome back. this one is a fun and simple python reversing challenge.

when we run the script, we get a menu with three options. choosing option **b (Print flag)** just throws a message saying the function is “missing” and tells us to check the source code. classic bait.

so instead of interacting with the program, we open the source file and start reading it.

looking through the code, we quickly find a function called `print_flag()` which is clearly not missing at all. inside it, we see this:

```python
flag = str_xor(flag_enc, 'enkidu')
```

so the flag is stored in an encoded form (`flag_enc`) and then decoded using a custom XOR function with the key `"enkidu"`.

the `str_xor` function simply repeats the key until it matches the length of the encrypted string and then XORs each character. nothing fancy.

at this point, there are two ways to solve it:
either manually reverse the XOR, or just call the function directly.

since this is python, we take the easy route and execute:

```bash
python3 -c "from serpentine import print_flag; print_flag()"
```

this bypasses the menu entirely and directly runs the hidden function.

and we get the flag:

```text
picoCTF{7h3_r04d_l355_7r4v3l3d_aa2340b2}
```

in the end, the challenge is just about not trusting the program flow and actually reading the source. the flag was never missing — it was just hidden behind a fake menu option.
