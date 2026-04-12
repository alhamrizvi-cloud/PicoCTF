In this challenge, we were given a remote service on picoCTF that accepts a Redcode program (Corewar).

Initially, we submitted a basic warrior:

```
mov 0, 1
```

This instruction continuously copies itself and behaves like a standard “imp” program, but it didn’t help us win the challenge.

After understanding the rules of Corewar, we realized that the goal was to make the opponent lose all rounds. Instead of trying to attack, we modified the instruction:

```
dat 0, 1
```

The `dat` instruction immediately terminates the process when executed. By doing this, our warrior instantly dies every round.

This may seem counterintuitive, but the challenge logic was designed such that losing all rounds triggers the success condition.

After submitting the modified code using:

```
nc saturn.picoctf.net 50002 < imp.red
```

We got the result:

* Warrior 1 wins: 0
* Warrior 2 wins: 100

This satisfied the challenge condition, and the flag was revealed.

### Flag

```
picoCTF{h3r0_t0_z3r0_4m1r1gh7_e476d4cf}
```
