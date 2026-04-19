This was one of the hardest challenges I’ve worked on. I wasn’t able to solve it directly at first, so I started digging into the binary and looking for anything interesting. Eventually, I found that there were chunks of data inside the file that looked like they might contain something useful.

To extract those parts, I used the following command:

```
for (( i=0; i < 10; i++)); do
  dd status=none if=./game count=10000 bs=1 skip=$((0x11a840 + $i*0x2720)) \
  | fold -w 100 -s
done
```

What this does is read specific offsets from the binary file and print them in a readable format. Each iteration skips to a different section of the file and dumps a chunk of data. Using `fold` helps format the output so it’s easier to visually inspect.

At first, the output didn’t look like a flag. Instead, it appeared to be ASCII art. That’s where things got tricky. The flag wasn’t directly visible as plain text you had to carefully read the ASCII art and interpret the characters.

By going through the output line by line and treating it as ASCII art, the hidden message (the flag) could be reconstructed. It wasn’t obvious at a glance, so patience was key here.

In the end, the challenge wasn’t about directly extracting the flag, but about recognizing that the data represented ASCII art and manually interpreting it to recover the final result.

