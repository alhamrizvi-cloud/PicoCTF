This challenge involves analyzing a Java program that validates a password using a byte array. The goal is to understand how the password is checked and reconstruct the correct input.


### Source Code Analysis

Inside the `checkPassword()` function, the following logic is used:

```java
byte[] passBytes = password.getBytes();
byte[] myBytes = {
    106 , 85  , 53  , 116 , 95  , 52  , 95  , 98  ,
    0x55, 0x6e, 0x43, 0x68, 0x5f, 0x30, 0x66, 0x5f,
    0142, 0131, 0164, 063 , 0163, 0137, 0145, 060 ,
    '2' , '1' , '3' , '8' , '7' , '2' , '1' , '3' ,
};
```

* The user input is converted into a byte array using:

  ```java
  password.getBytes()
  ```
* The program defines another byte array (`myBytes`) which represents the correct password.
* It then compares both arrays.

So, the correct password is simply the ASCII representation of `myBytes`.


### Understanding the Byte Array

The tricky part is that `myBytes` uses **mixed number formats**:

#### 1. Decimal values (base 10)

```text
106 → 'j'
85  → 'U'
53  → '5'
```

#### 2. Hexadecimal values (base 16, prefix `0x`)

```text
0x55 → 85  → 'U'
0x6e → 110 → 'n'
0x43 → 'C'
```

#### 3. Octal values (base 8, leading `0`)

```text
0142 → 98  → 'b'
0131 → 89  → 'Y'
0164 → 116 → 't'
```

#### 4. Character literals

```text
'2' → '2'
'1' → '1'
```

### Decoding Process

To solve the challenge:

1. Convert all values (decimal, hex, octal) into decimal
2. Map each decimal value to its ASCII character
3. Combine all characters into a string

After decoding the entire array, we get:

```text
jU5t_4_bUnCh_0f_bYt3s_e021387213
``'
