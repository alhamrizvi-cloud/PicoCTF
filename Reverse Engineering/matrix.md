

## Overview

This is a hard reverse engineering challenge. The binary uses a custom virtual machine (VM). Our goal is to understand how the VM works and give correct input so it prints the flag.


## Step 1: Understanding `main`

Open the binary in IDA and check the `main` function.

What it does:

* Allocates two buffers (memory for VM)
* Stores function pointers (`getc` and `putc`)
* Runs a loop that executes VM instructions (`sub_1350`)
* After execution:

  * If no error and value `v8 == 0`, it prints the flag

So the goal is simple:
We must give input that makes the VM exit cleanly with `v8 = 0`.

---

## Step 2: Identify VM Behavior

Two important functions:

```
sub_1320 → getc (reads input)
sub_1340 → putc (prints output)
```

Main VM logic is inside:

```
sub_1350 → instruction interpreter
```

This function:

* Reads one byte (opcode)
* Executes operation
* Returns:

  * `1` → continue
  * `0` → stop

---

## Step 3: Understand the VM Instructions

By reading the code, we identify instructions:

* `0xC0` → getc (read input)
* `0xC1` → putc (print output)
* `0x10` → dup
* `0x11` → pop
* `0x12` → add
* `0x13` → sub
* `0x30` → jump
* `0x31` → jump if zero
* `0x80` → push byte
* `0x81` → push word
* `0x01` → halt

This confirms it is a stack-based VM.

---

## Step 4: Extract the Bytecode

The VM runs instructions stored in memory (`unk_20F0`).

We dump this bytecode and save it as:

```
bytecode.bin
```

---

## Step 5: Build a Disassembler

We write a Python script to:

* Read bytecode
* Decode instructions
* Output readable assembly

After running it, we get a long assembly listing.

---

## Step 6: Analyze the Program

### Part 1: Welcome Message

The VM prints:

```
Welcome to the MATRIX
Can you make it out alive?
```

---

### Part 2: Input Handling

The VM reads one character:

```
getc()
```

Then checks if input is:

```
'u', 'd', 'l', 'r'
```

If not:

* It prints:

  ```
  You were eaten by a grue.
  ```
* Then exits with failure

---

### Part 3: Movement Logic

The VM uses your input as movement inside a grid.

We figure out position formula:

```
position = (x + y * 16) * 4 + base_address
```

This tells us:

* Grid width = 16
* Grid height = 16

---

## Step 7: Extract the Map

We analyze memory and convert it into a grid.

Legend:

```
# → wall
. → path
+ → health +1
- → damage -1
E → exit
S → start
```

Final map:

```
################
#S....+#.#+..#.#
####-###.###.#.#
#..........#.#.#
##.#.#####.#.-.#
#..#.#+..#.#.#.#
#.##.###.#.#.#.#
#.#......#.#.#.#
#.#.######.#.#.#
#.#........#.#.#
#.###.######.#.#
#...#..-..#..#.#
#.###..#..#.#..#
#.#...###.#.#-##
#.#....#..-.#..#
##############E#
```

---

## Step 8: Solve the Maze

Rules:

* Start at `S`
* Reach `E`
* Health must never go below 0
* `+` increases health
* `-` decreases health

We solve it using BFS (shortest path search).

---

## Step 9: Final Input

The correct movement string:

```
rrrrrlrlrlrlrllddddddlddrrddrrrdrddrruuuruuuuuuurrddddddddlddrd
```

---

## Step 10: Get the Flag

Send input to the program:

```
nc mars.picoctf.net 31259
```

Paste the moves → flag appears:

```
picoCTF{y0uv3_3sc4p3d_th3_m4ze...f0r_n0w-hYkq2D9PmrA5GpEq}
```
