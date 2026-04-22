
yo chads, quick one here. the challenge gives us a “song” file that just looks like weird lyrics at first. but the hint mentions *rockstar*, which is actually an esoteric programming language.

so instead of overthinking it, i searched for a Rockstar interpreter, pasted the lyrics, and ran it. no errors — just a bunch of numbers as output:

```
114 114 114 111 99 107 110 114 110 48 49 49 51 114
```

this immediately hints at ASCII. converting each number to its ASCII character gives:

```
rrrocknrn0113r
```

since picoCTF flags are in the format `picoCTF{}`, we wrap it:

```
picoCTF{rrrocknrn0113r}
```

the provided base64 version:

```
cGljb0NURntycnJvY2tucm4wMTEzcn0=
```
