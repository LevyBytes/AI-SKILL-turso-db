# Math atan2 ceiling and cosine

[Context index](math-function-reference.md) · [Setup and preceding context](math-function-and-signature-reference.md) · [Previous phase](math-inverse-trigonometric-and-hyperbolic-functions.md) · [Continue](math-degrees-exponentials-and-floor.md)

### atan2()

```
atan2(Y, X)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `Y` | REAL or INTEGER | The y-coordinate |
| `X` | REAL or INTEGER | The x-coordinate |

 **Returns:** REAL. The radian angle from the positive x-axis to point (X, Y). The output range is (-pi, pi]. Unlike `atan(Y/X)`, `atan2` resolves every quadrant correctly and also handles X equal to zero.

---

### atanh()

```
atanh(X)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `X` | REAL or INTEGER | Value between -1.0 and 1.0 (exclusive) |

 **Returns:** REAL. The inverse hyperbolic tangent for X. Produces NULL when X is not inside (-1.0, 1.0).

---

### ceil() / ceiling()

```
ceil(X)
ceiling(X)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `X` | REAL or INTEGER | Any numeric value |

 **Returns:** INTEGER for integer input; REAL for floating-point input. The lowest integer that is not below X.

```
SELECT ceil(3.2);    -- 4
SELECT ceil(-3.2);   -- -3
SELECT ceiling(5.0); -- 5

```

---

### cos()

```
cos(X)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `X` | REAL or INTEGER | Angle in radians |

 **Returns:** REAL. Computes cos(X).

---

### cosh()

```
cosh(X)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `X` | REAL or INTEGER | Any numeric value |

 **Returns:** REAL. Computes X's hyperbolic cosine.

---
