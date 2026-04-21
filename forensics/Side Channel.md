### SideChannel — Writeup 

We are given a binary called `pin_checker` and told that something is “fishy” about how it checks an 8-digit PIN. The goal is simple: find the correct PIN and use it to retrieve the flag from the remote server.

### Understanding the Vulnerability

When we run the binary normally, it asks for an 8-digit PIN and responds with either “Access denied” or success.

The hint points toward a **timing-based side-channel attack**. This means:

> The program takes slightly longer to check correct digits than incorrect ones.

This usually happens when a program compares input **digit by digit**, stopping as soon as it finds a mismatch.

So:

* If the first digit is correct → it checks the second → takes more time
* If first digit is wrong → it exits immediately → less time

This difference in execution time leaks information.

---

### Manual Observation

We test execution time using:

```bash
time echo 10000000 | ./pin_checker
```

Then try another:

```bash
time echo 40000000 | ./pin_checker
```

We notice:

* Some inputs take **longer** than others
* The longer time indicates **more correct prefix digits**

From this, we deduce:

> The correct PIN can be built one digit at a time by choosing the digit that causes the longest execution time.

---

### Automating the Attack

Instead of guessing manually, we automate it using a script.

Idea:

* Start with `00000000`
* For each position (0 → 7):

  * Try digits `0–9`
  * Measure execution time
  * Pick the digit with **maximum time**

---

### Exploit Script Explanation

The script does the following:

1. Initializes PIN as `00000000`
2. For each digit position:

   * Tries all digits `0–9`
   * Runs the binary
   * Measures execution time
3. Picks the digit that took the longest
4. Moves to the next position

Key part:

```python
correct_digit = max(range(len(time_results)), key=time_results.__getitem__)
```

This selects the digit with the highest execution time.

---

### Result

Running the script reveals:

```text
PIN = 48390513
```

---

### Getting the Flag

Now connect to the server:

```bash
nc saturn.picoctf.net 55824
```

Enter the PIN:

```text
48390513
```

The server validates it and returns the flag.

---

### Key Takeaway

This challenge demonstrates a classic issue:

> Even if logic is correct, leaking timing information can break security.

Programs should:

* Use constant-time comparisons
* Avoid early exits during validation

---

### Final Note

No reverse engineering of assembly was needed here. Just observing behavior carefully and exploiting timing differences was enough to fully break the system.
