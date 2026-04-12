This challenge is a simple logic bug exploitation in a shop system where you can buy and sell items using coins.

At the start, you only have 40 coins, and the flag item (“Fruitful Flag”) costs 100 coins, so normally you cannot afford it. The vulnerability is in the input handling when buying items. The program does not properly validate negative numbers.

When you chose to buy item (1) “Average Apple” and entered `-10` as the quantity, the program incorrectly processed it. Instead of rejecting the input, it treated it in a way that increased your coins. This is an integer logic flaw where buying a negative quantity effectively gives you money instead of subtracting it. Because of this, your coins increased from 40 to 190.

<img width="1366" height="693" alt="image" src="https://github.com/user-attachments/assets/ead2f83c-7bbf-41fd-b5bf-f79a027ef480" />

After exploiting this bug, you now had enough coins to buy the flag. You then selected option (2) and bought 1 “Fruitful Flag” for 100 coins.

Instead of printing the flag directly as a string, the program returned it as a list of ASCII values:
[112 105 99 111 67 84 70 123 98 52 100 95 98 114 111 103 114 97 109 109 101 114 95 48 54 53 56 98 54 50 57 101 125 10]

Converting these decimal ASCII values to characters gives:
<img width="1089" height="404" alt="image" src="https://github.com/user-attachments/assets/7a90257a-6ae3-47f1-ba43-c91327c355b8" />

picoCTF{b4d_brogrammer_0658b629e}

So the full process was:
first exploit the negative quantity bug to gain extra coins, then buy the flag item, and finally convert the returned ASCII values into a readable string to get the flag.
