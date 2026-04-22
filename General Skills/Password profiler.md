yo chads, welcome back to another clean writeup. this one is a classic OSINT + wordlist attack, nothing too crazy but really good to understand how real-world password guessing works.

so the challenge gives us a SHA-1 hash instead of the actual password. brute forcing blindly would take forever, so the trick is to **use personal information about the target** to generate smart guesses. this is exactly where tools like CUPP come in.

we start by running `cupp.py -i`, which puts us into interactive mode. here we feed in all the victim’s details from the provided `userinfo` file — name, nickname, birthdate, partner info, etc. even if some fields look useless, include them anyway because password patterns often combine these in weird ways.

after answering the prompts (and enabling options like special characters, random numbers, and leetspeak), CUPP generates a custom wordlist. in this case, it creates a file called `alice.txt` with around 33k possible passwords. this is way more efficient than generic wordlists because it’s tailored to the target.

next step is just preparing the wordlist. we move `alice.txt` to the main challenge directory and rename it to `passwords.txt` so it works with the provided script.

finally, we run `check_password.py`, which hashes each wordlist entry and compares it with the given SHA-1 hash. after a short run, it hits the correct match:

`picoCTF{Aj_15901990}`

so yeah, the whole challenge is about understanding that **people reuse personal info in passwords**, and tools like CUPP automate exploiting that weakness. instead of brute force, you’re doing *smart guessing* based on OSINT.

gg, onto the next one.
