# Math hyperbolic sine root and tangent

[Context index](math-function-reference.md) · [Setup and preceding context](math-function-and-signature-reference.md) · [Previous phase](math-powers-radians-sign-and-sine.md) · [Continue](math-truncation-and-calculation-examples.md)

### sinh()

```
sinh(X)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `X` | REAL or INTEGER | Any numeric value |

 **Returns:** REAL. Computes X's hyperbolic sine.

---

### sqrt()

```
sqrt(X)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `X` | REAL or INTEGER | Value >= 0 |

 **Returns:** REAL. Computes the square root of X. Produces NULL when X is negative.

```
SELECT sqrt(144);  -- 12.0
SELECT sqrt(2);    -- 1.4142135623730951

```

---

### tan()

```
tan(X)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `X` | REAL or INTEGER | Angle in radians |

 **Returns:** REAL. Computes tan(X).

---

### tanh()

```
tanh(X)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `X` | REAL or INTEGER | Any numeric value |

 **Returns:** REAL. Computes X's hyperbolic tangent. The result always lies within (-1.0, 1.0).

---
