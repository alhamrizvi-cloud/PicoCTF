```
alhamrizvi@alhams-fedora:~/Downloads$ nc crystal-peak.picoctf.net 50029
What is 4 + 9? 13
Encoded flag values:
1456, 1365, 1287, 1443, 871, 1092, 910, 1599, 1417, 676, 1508, 1352, 1235, 1274, 663, 1352, 637, 1430, 1300, 1235, 1287, 637, 1456, 1352, 663, 1482, 1235, 663, 637, 728, 689, 1313, 1300, 650, 1313, 1625
```

Looking at this, the encoded values are likely the flag characters multiplied by some key derived from the math answer (13).

Let me check: if we divide each value by 13:

1456/13 = 112 = 'p'
1365/13 = 105 = 'i'
1287/13 = 99 = 'c'
1443/13 = 111 = 'o'
871/13 = 67 = 'C'
1092/13 = 84 = 'T'
910/13 = 70 = 'F'
1599/13 = 123 = '{'

That's `picoCTF{` — we're on the right track! Let me decode the rest:The flag is:

```
picoCTF{m4th_b3h1nd_c1ph3r_3185ed2e}
```

**How it works:** The server asked a math question (`4 + 9 = 13`), then used that answer as a multiplicative key — each character's ASCII value was simply multiplied by **13**. To decode, you divide each encoded value by 13 and convert back to a character with `chr()`.
