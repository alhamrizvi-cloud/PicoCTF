
This was an easy reverse engineering challenge. The goal was to understand how the program validates the password and reconstruct the correct input.

The program takes input in the format:

```text
picoCTF{...}
```

It extracts the inner 32-character string and passes it to the `checkPassword()` function.

Inside `checkPassword()`, a `buffer` array is constructed using different transformations on the input password:

* First 8 characters are copied directly
* Next 8 are reversed from another section
* Remaining characters are rearranged using specific index mappings

Finally, the program compares the result with:

```text
jU5t_a_sna_3lpm1cg04e_u_4_m6rb42
```

### Trick Used

Instead of manually reversing all the transformations, I modified the code by adding:

```java
System.out.println(buffer);
```

This prints the **final transformed string** directly during execution.

After compiling and running the program, I entered any dummy input like:

```text
picoCTF{aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa}
```

The program printed:

```text
jU5t_a_sna_3lpm1cg04e_u_4_m6rb42
```

This revealed the exact string that the program expects after transformation

Now that the target string is known, I reversed the transformation logic to reconstruct the original password.

The correct password is:

```text
jU5t_a_s1mpl3_an4gr4m_4_u_c0d3r
```

### Final Flag

```text
picoCTF{jU5t_a_s1mpl3_an4gr4m_4_u_c0d3r}
```

--
