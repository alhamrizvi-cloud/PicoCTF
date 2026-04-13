Another cool challenge. At first, the Python code looks very messy with a lot of unnecessary “blah blah” obfuscation.

Instead of getting confused, I focused on the important parts — especially how the program checks the password.

While analyzing the code, I noticed a loop that checks whether the entered password is correct. But inside the `else` condition, something suspicious happens:

* It prints *"That password is incorrect"*
* Then calls `sys.exit(0)`
* And after that, it has `return False`

This is clearly weird because `sys.exit(0)` stops the program immediately, so `return False` will never run.

This means the program exits before continuing, which prevents us from reaching the flag.

I simply **commented out the `sys.exit(0)` line**.

After removing that, even if the password is incorrect, the program continues execution and eventually reaches the decryption part.

After running the modified script, it printed:

```
Welcome back... your flag, user:
picoCTF{d30bfu5c4710n_f7w_5e14b257}
```
