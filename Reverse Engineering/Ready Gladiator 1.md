
This challenge is based on **Core War**, where two programs (called warriors) are loaded into memory and run at the same time. The goal is to make the opponent’s program crash. In this game, if a program executes a `DAT` instruction, it dies immediately. So the main idea is to overwrite the opponent’s code with `DAT` so it eventually crashes.

kill.red

```
bomb    DAT  #0,   #0
step    EQU  653

start   ADD  #step, bomb
        MOV  bomb,  @bomb
        JMP  start
```

Here’s what each part does:

* `bomb DAT #0, #0`
  This defines a deadly instruction. If any process executes this, it dies.

* `step EQU 653`
  This is a constant value. It controls how far apart your attacks are in memory. 653 is chosen because it spreads attacks across the core efficiently.

* `ADD #step, bomb`
  This keeps changing the address stored in `bomb`. Basically, it moves the target location forward each loop.

* `MOV bomb, @bomb`
  This is the key part. It copies the `DAT` instruction into another memory location (pointed to indirectly by `bomb`). So you are “bombing” memory with kill instructions.

* `JMP start`
  This loops forever, so your program keeps spreading bombs continuously.

So what your program is doing is:
It keeps jumping around memory and writing `DAT` instructions everywhere. Eventually, the opponent’s program hits one of these `DAT` instructions and dies.

Now how you got the flag:

1. You saved this code in a file (`kill.red`).
2. You sent it to the challenge server using netcat:

   ```
   nc saturn.picoctf.net 51350 < kill.red
   ```
3. The server runs your warrior vs their warrior for 100 rounds.
4. Output showed:

   * You won 35 rounds
   * Opponent won 0
   * Rest were ties

Since you never lost and won enough rounds, the server considered it a success.

5. After that, it printed:

   ```
   You did it!
   picoCTF{1mp_1n_7h3_cr055h41r5_0b0942be}
   ```

That’s the flag.

In short: you used a looping bomb strategy to overwrite memory with `DAT`, which killed the opponent program during execution, and that gave you enough wins to get the flag.
