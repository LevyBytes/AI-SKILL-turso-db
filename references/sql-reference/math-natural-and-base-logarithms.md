# Math natural and base logarithms

[Context index](math-function-reference.md) · [Setup and preceding context](math-function-and-signature-reference.md) · [Previous phase](math-degrees-exponentials-and-floor.md) · [Continue](math-binary-log-modulus-and-pi.md)

### ln()

```
ln(X)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `X` | REAL or INTEGER | Value > 0 |

 **Returns:** REAL. X's natural logarithm, using base e. Produces NULL when X <= 0.

```
SELECT ln(1);         -- 0.0
SELECT ln(exp(1));    -- 1.0
SELECT ln(2);         -- 0.6931471805599453

```

---

### log()

```
log(X)
log(B, X)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `X` | REAL or INTEGER | Value > 0 |
| `B` | REAL or INTEGER | Base > 0 and != 1 (optional) |

 **Returns:** REAL. Given one argument, returns X's base-10 logarithm, equivalent to `log10(X)`. Given two arguments, returns the base B logarithm of X.

```
SELECT log(10);      -- 1.0 (base-10 log)
SELECT log(10, 100); -- 2.0 (log base 10 of 100)
SELECT log(2, 256);  -- 8.0 (log base 2 of 256)

```

---

### log10()

```
log10(X)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `X` | REAL or INTEGER | Value > 0 |

 **Returns:** REAL. X's base-10 logarithm. Produces NULL when X <= 0.

```
SELECT log10(100);   -- 2.0
SELECT log10(1000);  -- 3.0

```

---
