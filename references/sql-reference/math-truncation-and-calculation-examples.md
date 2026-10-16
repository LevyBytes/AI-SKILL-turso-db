# Math truncation and calculation examples

[Context index](math-function-reference.md) · [Setup and preceding context](math-function-and-signature-reference.md) · [Previous phase](math-hyperbolic-sine-root-and-tangent.md)

### trunc()

```
trunc(X)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `X` | REAL or INTEGER | Any numeric value |

 **Returns:** INTEGER for integer input; REAL for floating-point input. X with the fractional portion removed, truncating in the direction of zero.

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
