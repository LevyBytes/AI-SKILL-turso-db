# Math powers radians sign and sine

[Context index](math-function-reference.md) · [Setup and preceding context](math-function-and-signature-reference.md) · [Previous phase](math-binary-log-modulus-and-pi.md) · [Continue](math-hyperbolic-sine-root-and-tangent.md)

### pow() / power()

```
pow(X, Y)
power(X, Y)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `X` | REAL or INTEGER | Base |
| `Y` | REAL or INTEGER | Exponent |

 **Returns:** REAL. X raised to exponent Y.

```
SELECT pow(2, 10);      -- 1024.0
SELECT power(3, 3);     -- 27.0
SELECT pow(100, 0.5);   -- 10.0 (square root)

```

---

### radians()

```
radians(X)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `X` | REAL or INTEGER | Angle in degrees |

 **Returns:** REAL. Converts X from degrees into radians.

```
SELECT radians(180);   -- 3.141592653589793
SELECT radians(90);    -- 1.5707963267948966

```

---

### sign()

```
sign(X)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `X` | REAL or INTEGER | Any numeric value |

 **Returns:** INTEGER. Produces -1 when X is negative, 0 when X is zero, and +1 when X is positive. Produces NULL when X is NULL.

```
SELECT sign(-42);  -- -1
SELECT sign(0);    -- 0
SELECT sign(3.14); -- 1

```

---

### sin()

```
sin(X)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `X` | REAL or INTEGER | Angle in radians |

 **Returns:** REAL. Computes sin(X).

---
