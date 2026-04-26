
We connect via netcat and get three values:

```
c: 1337795478303509200628718416769016160598771442913710679639703788535326628932573756878630514188658907538871478248947216476195029263630989663690292595701974787335281725660963081746989451290613117576152681046913115273738742765440178829294985710066196906936658471894827668006382217590478034950015188616718769943
n: 1061458817577652246471376508147573569130337364816464841124760445354583881237119704585042862894184218394717969615442777249780309867575593259757998989668682038794312543774626031647828445696475185631187492679271006198416767824008484813651278522976864814211910416576521960390470415620263099038033711454869430600
e: 3877239213572702004711580058370807890414749862877664804871391778894881293457956361759210994525821445008339331160120946119366287428459057791071720984127832274847781627023175264798349602341894836031003481212990195182083820590546245045396593824170513056688560339077258648511240302141799807187473195798412655903
```

The challenge asks: *"In RSA, d is a lot bigger than e — why don't we use d to encrypt instead of e?"*

That's the hint. They swapped `e` and `d`.

---

## Understanding the Vulnerability

Normal RSA:
- `e` = small (usually 65537)
- `d` = huge (hard to find without knowing `p` and `q`)

This challenge:
- `d` = 65537 (tiny!)
- `e` = huge

**Why is small `d` dangerous?** Wiener's Theorem (1990) proves that if:

```
d < (1/3) * n^(1/4)
```

Then `d` can be recovered just from the public key `(e, n)` using continued fractions — no factoring needed.

Here `d = 65537` which is tiny compared to `n`, so the attack works perfectly.

---

## How Wiener's Attack Works

RSA has this relationship:

```
e * d = 1 mod φ(n)
```

Which means for some integer `k`:

```
e * d = k * φ(n) + 1
```

Rearranging:

```
e/n ≈ k/d
```

So `k/d` is a close approximation of `e/n`. The **continued fraction expansion** of `e/n` produces a list of fractions that get progressively closer to the true value — and `k/d` will appear in that list if `d` is small enough.

For each candidate `(k, d)` from the continued fractions, we test if it's the real `d` by:
1. Computing `φ(n) = (e*d - 1) / k`
2. Solving for `p` and `q` using the quadratic: `x² - (n - φ(n) + 1)x + n = 0`
3. Checking if `p * q == n`

If it checks out, we found `d`!

---

## The Script

```python
from math import isqrt

def continued_fraction(n, d):
    while d:
        yield n // d
        n, d = d, n % d

def convergents(cf):
    n0, n1 = 1, 0
    d0, d1 = 0, 1
    for q in cf:
        n0, n1 = q*n0 + n1, n0
        d0, d1 = q*d0 + d1, d0
        yield n0, d0

def isqrt_exact(n):
    r = isqrt(n)
    return r if r*r == n else None

def wiener(e, n):
    for k, d in convergents(continued_fraction(e, n)):
        if k == 0:
            continue
        if (e * d - 1) % k != 0:
            continue
        phi = (e * d - 1) // k
        b = n - phi + 1
        disc = b*b - 4*n
        if disc < 0:
            continue
        sq = isqrt_exact(disc)
        if sq is None:
            continue
        p = (b + sq) // 2
        q = (b - sq) // 2
        if p * q == n:
            return d
    return None

n = 106145881757765224647137650814757356913033736481646484112476044535458388123711970458504286289418421839471796961544277724978030986757559325975799898966868203879431254377462603164782844569647518563118749267927100619841676782400848481365127852297686481421191041657652196039047041562026309903803371145486943060083
e = 38772392135727020047115800583708078904147498628776648048713917788948812934579563617592109945258214450083393311601209461193662874284590577910717209841278322748477816270231752647983496023418948360310034812129901951820838205905462450453965938241705130566885603390772586485112403021417998071874731957984126559033
c = 13377954783035092006287184167690161605987714429137106796397037885353266289325737568786305141886589075388714782489472164761950292636309896636902925957019747873352817256609630817469894512906131175761526810469131152737387427654401788292949857100661969069366584718948276680063822175904780349500151886167187699434

d = wiener(e, n)
print(f"Recovered d = {d}")  # d = 65537 !

m = pow(c, d, n)
h = hex(m)[2:]
if len(h) % 2: h = '0' + h
print(bytes.fromhex(h).decode())
```

---

## Output

```
Recovered d = 65537
picoCTF{bad_1d3a5_3801255}
```

The recovered `d` is exactly 65537 — the value that should have been `e` all along.
