
Inside `checkPassword()`:

```java
String urlEncoded = urlEncode(password.getBytes());
String base64Encoded = base64Encode(urlEncoded.getBytes());
return base64Encoded.equals(expected);
```

So the flow is:

```text
password → URL encode → Base64 encode → compare with expected
```

Reverse it:

```text
expected → Base64 decode → URL decode → original password
```


### 1. Take the expected string

```text
JTYzJTMwJTZlJTc2JTMzJTcyJTc0JTMxJTZlJTY3JTVmJTY2JTcyJTMwJTZkJTVmJTYyJTYxJTM1JTY1JTVmJTM2JTM0JTVmJTM0JTMyJTYzJTM2JTM0JTMwJTM5JTYy
```

### 2. Base64 decode it

After decoding, you get something like:

```text
%63%30%6e%76%33%72%74%31%6e%67%5f%66%72%30%6d%5f%62%61%35%65%5f%36%34%5f%34%32%63%36%34%30%39%62
```

### 3. URL decode it

Each `%xx` is hex → ASCII:

Examples:

```text
%63 → 'c'
%30 → '0'
%6e → 'n'
```

After decoding everything:

```text
c0nv3rt1ng_fr0m_ba5e_64_42c6409b
```

## Final Flag

```text
picoCTF{c0nv3rt1ng_fr0m_ba5e_64_42c6409b}
```
