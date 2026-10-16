# Math degrees exponentials and floor

[Context index](math-function-reference.md) · [Setup and preceding context](math-function-and-signature-reference.md) · [Previous phase](math-atan2-ceiling-and-cosine.md) · [Continue](math-natural-and-base-logarithms.md)

### degrees()

```
degrees(X)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `X` | REAL or INTEGER | Angle in radians |

 **Returns:** REAL. Converts X from radians into degrees.

```
SELECT degrees(pi());   -- 180.0
SELECT degrees(pi()/2); -- 90.0

```

---

### exp()

```
exp(X)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `X` | REAL or INTEGER | Exponent |

 **Returns:** REAL. e raised to exponent X.

```
SELECT exp(0);   -- 1.0
SELECT exp(1);   -- 2.718281828459045

```

---

### floor()

```
floor(X)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `X` | REAL or INTEGER | Any numeric value |

 **Returns:** INTEGER for integer input; REAL for floating-point input. The highest integer that is not above X.

```
SELECT floor(3.7);   -- 3
SELECT floor(-3.2);  -- -4
SELECT floor(5.0);   -- 5

```

---
