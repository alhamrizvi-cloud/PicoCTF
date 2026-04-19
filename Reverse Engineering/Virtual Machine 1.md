### Challenge: Virtual machine 1.md
This challenge was honestly one of my nightmares, I almost gave up on it five times, I kept retrying the virtual machine 0 and then 1 part again and again, but I couldn’t even solve the first step.

I spent a lot of time and resources on it, including trying different AI tools, but nothing really helped. I even looked for writeups, yet I still couldn’t fully understand how to approach it. At one point, it felt nearly impossible.

But I didn’t quit. I kept pushing through, testing different ideas, failing, and trying again. It was frustrating, but I was determined to figure it out on my own.

In the end, even though the challenge felt impossible, I managed to get through it. This one really tested my patience more than my technical skills.

 ## Basiclly what's going on lol
We are given a design document of a machine with many gears. The hints tell us that:

* Some supporting structures are removed
* Some gears look incorrect but should be treated as valid
* We need to understand gear ratios to solve it

After downloading the file, I opened it in a 3D viewer.

The structure contains a very complex gear system with different types of gear setups:

* Simple gear train (two gears directly connected)
* Compound gears (gears of different sizes on the same shaft)
* Multi-stage gear train (gears connected in multiple steps)
* Differential gears (two branches combined into one output)

Because of this, we cannot use just one formula. We need to calculate ratios step by step depending on the type of connection

Each gear system has its own formula:

* Simple Gear Train
  Ratio = N(driver) / N(driven)

* Idler Gear
  Only changes direction, does not affect ratio

* Compound Gear
  Total Ratio = (N₁ / N₂) × (N₃ / N₄)

* Multi-stage Gear
  Total Ratio = Stage 1 × Stage 2

* Differential Gear
  Combine both branches and take the average ratio

(N = number of teeth on a gear)

By carefully following the gear connections from the red shaft (input) to the blue shaft (output), and applying the correct formulas at each step, we can compute the final ratio.

After simplifying everything, the final ratio becomes:

1 : 9359

This means:

* If the red shaft rotates once
* The blue shaft rotates 9359 times

Because the structure is very complex, doing all calculations manually is difficult. The final ratio can be confirmed from references or by carefully scripting the computation.

### Exploit

We connect to the server using nc.

The server gives a random integer input:

* “If the input rotates X times, what is the output?”

Since we already know the ratio is 1:9359, the logic is simple:

Output = X × 9359

So for any given input, we just multiply it by 9359 and submit the result.


### Conclusion

This challenge looks complicated because of the large gear system, but the main idea is to:

* Break the system into smaller parts
* Apply the correct gear ratio formulas
* Combine everything into one final ratio

Once the ratio is known, the rest of the challenge becomes very simple.
