# Math binary log modulus and pi

[Context index](math-function-reference.md) · [Setup and preceding context](math-function-and-signature-reference.md) · [Previous phase](math-natural-and-base-logarithms.md) · [Continue](math-powers-radians-sign-and-sine.md)

### log2()

```
log2(X)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `X` | REAL or INTEGER | Value > 0 |

 **Returns:** REAL. X's base-2 logarithm. Produces NULL when X <= 0.

```
SELECT log2(256);   -- 8.0
SELECT log2(1024);  -- 10.0

```

---

### mod()

```
mod(X, Y)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `X` | REAL or INTEGER | Dividend |
| `Y` | REAL or INTEGER | Divisor |

 **Returns:** REAL. The remainder from X divided by Y. Produces NULL when Y is 0.

```
SELECT mod(10, 3);    -- 1.0
SELECT mod(10.5, 3);  -- 1.5
SELECT mod(-10, 3);   -- -1.0

```

---

### pi()

```
pi()

```

 Accepts no arguments. **Returns:** REAL. The mathematical constant pi, 3.141592653589793.

```
SELECT pi();           -- 3.141592653589793
SELECT 2 * pi() * 5;  -- 31.41592653589793 (circumference of circle with radius 5)

```

---
