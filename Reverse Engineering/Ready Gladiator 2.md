This challenge was another Core War task where we had to submit a Redcode warrior that can consistently defeat the opponent. The goal is to make the enemy program crash while keeping our own program alive.

The solution used a very small but effective program:

```
jmp 0, <-2
```

This instruction creates a tight infinite loop. The `JMP 0` keeps execution at the same instruction, so the program never moves forward or executes anything else. The `<-2` part uses pre-decrement indirect addressing, which keeps modifying nearby memory locations as the instruction runs.

Because of this behavior, the program acts like a defensive trap. It keeps looping forever while also interfering with memory around it. This makes it difficult for the opponent program (especially simple “imp” style programs that move linearly through memory) to survive, as they eventually run into corrupted instructions or invalid states.

To solve the challenge, I saved this code in a file and sent it to the remote service using netcat. The server runs 100 rounds between both warriors. In this case, the program won all 100 rounds, completely defeating the opponent.

Since the solution achieved consistent wins, the challenge returned the flag after execution.
