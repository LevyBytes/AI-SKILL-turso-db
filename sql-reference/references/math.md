# Math

> Sql Reference reference. Original prose; identifiers preserved verbatim.

### Math Functions

 Turso includes the complete SQLite math function set. These routines evaluate numeric inputs and normally produce REAL results unless a specific entry states otherwise. A NULL input makes the result NULL. When an argument is not numeric and cannot be coerced, the result varies by routine: NULL or 0.0.

### Function Reference

### Trigonometric Functions

| Function | Description | Return Type |
| --- | --- | --- |
| `acos(X)` | Returns the arccosine of X, measured in radians. Valid X values run from -1.0 through 1.0. | REAL |
| `asin(X)` | Returns the arcsine of X as a radian value. X has to be within -1.0 and 1.0. | REAL |
| `atan(X)` | Computes the arctangent of X, expressed in radians. | REAL |
| `atan2(Y, X)` | Computes the radian arctangent for Y/X, with both signs used to select the correct quadrant. | REAL |
| `cos(X)` | Computes cos(X), with X interpreted as radians. | REAL |
| `sin(X)` | Computes sin(X), treating X as a radian angle. | REAL |
| `tan(X)` | Computes tan(X), where X is supplied in radians. | REAL |

### Hyperbolic Functions

| Function | Description | Return Type |
| --- | --- | --- |
| `acosh(X)` | Computes the inverse hyperbolic cosine for X. Requires X >= 1.0. | REAL |
| `asinh(X)` | Inverse hyperbolic sine of X | REAL |
| `atanh(X)` | Computes the inverse hyperbolic tangent for X. X must fall between -1.0 and 1.0. | REAL |
| `cosh(X)` | Hyperbolic cosine of X | REAL |
| `sinh(X)` | Hyperbolic sine of X | REAL |
| `tanh(X)` | Hyperbolic tangent of X | REAL |

### Angle Conversion

| Function | Description | Return Type |
| --- | --- | --- |
| `degrees(X)` | Changes X from a radian angle into degrees. | REAL |
| `radians(X)` | Changes X from a degree angle into radians. | REAL |

### Rounding and Truncation

| Function | Description | Return Type |
| --- | --- | --- |
| `ceil(X)` | The least integer value that is still not below X. | INTEGER |
| `ceiling(X)` | Alias for `ceil(X)` | INTEGER |
| `floor(X)` | The greatest integer value that does not exceed X. | INTEGER |
| `trunc(X)` | The whole-number portion of X, with truncation moving toward zero. | INTEGER |

### Logarithmic and Exponential Functions

| Function | Description | Return Type |
| --- | --- | --- |
| `exp(X)` | Raises e to exponent X. | REAL |
| `ln(X)` | Computes the natural log of X. X must be > 0. | REAL |
| `log(X)` | Computes X's natural log, equivalent to `ln(X)`. | REAL |
| `log(B, X)` | Computes the logarithm of X using base B. Both values must be > 0, and B != 1. | REAL |
| `log10(X)` | Computes the base-10 log of X. X must be > 0. | REAL |
| `log2(X)` | Computes the base-2 log of X. X must be > 0. | REAL |

 For one argument, `log(X)` computes the natural logarithm, using base e, in the same way SQLite does. Some database systems instead interpret `log(X)` as base-10.

### Arithmetic Functions

| Function | Description | Return Type |
| --- | --- | --- |
| `mod(X, Y)` | The remainder after dividing X by Y. | REAL |
| `pow(X, Y)` | Raises X to exponent Y. | REAL |
| `power(X, Y)` | Alias for `pow(X, Y)` | REAL |
| `sqrt(X)` | Computes X's square root. Requires X >= 0. | REAL |
| `sign(X)` | Produces -1 for negative X, 0 for zero X, or +1 for positive X. | INTEGER |

### Constants

| Function | Description | Return Type |
| --- | --- | --- |
| `pi()` | Returns the value of pi (3.141592653589793) | REAL |

### Signatures

### acos()

```
acos(X)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `X` | REAL or INTEGER | Value between -1.0 and 1.0 |

 **Returns:** REAL. The radian angle with cosine X. Produces NULL when X is not within [-1.0, 1.0].

---

### acosh()

```
acosh(X)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `X` | REAL or INTEGER | Value >= 1.0 |

 **Returns:** REAL. The inverse hyperbolic cosine for X. Produces NULL when X < 1.0.

---

### asin()

```
asin(X)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `X` | REAL or INTEGER | Value between -1.0 and 1.0 |

 **Returns:** REAL. The radian angle with sine X. Produces NULL when X is not within [-1.0, 1.0].

---

### asinh()

```
asinh(X)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `X` | REAL or INTEGER | Any numeric value |

 **Returns:** REAL. Computes X's inverse hyperbolic sine.

---

### atan()

```
atan(X)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `X` | REAL or INTEGER | Any numeric value |

 **Returns:** REAL. The radian angle whose tangent equals X. The output falls in (-pi/2, pi/2).

---

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

 **Returns:** INTEGER. The lowest integer that is not below X.

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

 **Returns:** INTEGER. The highest integer that is not above X.

```
SELECT floor(3.7);   -- 3
SELECT floor(-3.2);  -- -4
SELECT floor(5.0);   -- 5

```

---

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

 **Returns:** REAL. Given one argument, returns X's natural logarithm, equivalent to `ln(X)`. Given two arguments, returns the base B logarithm of X.

```
SELECT log(10);      -- 2.302585... (natural log)
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

### trunc()

```
trunc(X)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `X` | REAL or INTEGER | Any numeric value |

 **Returns:** INTEGER. X with the fractional portion removed, truncating in the direction of zero.

```
SELECT trunc(3.7);   -- 3
SELECT trunc(-3.7);  -- -3
SELECT trunc(5.0);   -- 5

```

### Examples

### Trigonometry

```
-- Sine and cosine of 45 degrees
SELECT sin(radians(45));  -- 0.7071067811865475
SELECT cos(radians(45));  -- 0.7071067811865476

-- Angle from coordinates
SELECT degrees(atan2(1, 1));  -- 45.0

-- Verify the Pythagorean identity: sin^2 + cos^2 = 1
SELECT pow(sin(1.0), 2) + pow(cos(1.0), 2);  -- 1.0

```

### Rounding

```
-- Different rounding behaviors
SELECT ceil(2.3), floor(2.3), trunc(2.3);
-- 3, 2, 2

SELECT ceil(-2.3), floor(-2.3), trunc(-2.3);
-- -2, -3, -2

```

### Logarithms

```
-- Compute the number of bits needed to represent N
SELECT ceil(log2(1000));  -- 10

-- Decibel conversion
SELECT 10 * log10(100);  -- 20.0

-- Verify log identities
SELECT ln(exp(5));  -- 5.0

```

### Distance Calculation

```
-- Euclidean distance between two points
SELECT sqrt(pow(3 - 0, 2) + pow(4 - 0, 2));
-- 5.0 (3-4-5 triangle)

```

### See Also

- [Expressions](https://docs.turso.tech/sql-reference/expressions) covers arithmetic operators
- [Aggregate Functions](https://docs.turso.tech/sql-reference/functions/aggregate) covers SUM, AVG, MIN, MAX
- [Data Types](https://docs.turso.tech/sql-reference/data-types) covers REAL and INTEGER storage classes
