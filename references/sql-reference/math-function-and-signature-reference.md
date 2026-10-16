# Math function and signature reference

[Context index](math-function-reference.md) · [Continue](math-inverse-trigonometric-and-hyperbolic-functions.md)

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
| `ceil(X)` | The least integer value that is still not below X. | INTEGER or REAL |
| `ceiling(X)` | Alias for `ceil(X)` | INTEGER or REAL |
| `floor(X)` | The greatest integer value that does not exceed X. | INTEGER or REAL |
| `trunc(X)` | The whole-number portion of X, with truncation moving toward zero. | INTEGER or REAL |

### Logarithmic and Exponential Functions

| Function | Description | Return Type |
| --- | --- | --- |
| `exp(X)` | Raises e to exponent X. | REAL |
| `ln(X)` | Computes the natural log of X. X must be > 0. | REAL |
| `log(X)` | Computes X's base-10 logarithm, equivalent to `log10(X)`. | REAL |
| `log(B, X)` | Computes the logarithm of X using base B. Both values must be > 0, and B != 1. | REAL |
| `log10(X)` | Computes the base-10 log of X. X must be > 0. | REAL |
| `log2(X)` | Computes the base-2 log of X. X must be > 0. | REAL |

 With one argument, `log(X)` uses base 10, matching SQLite. Use `ln(X)` for base e. The inspected Turso build returns NULL for a two-argument base between 0 and 1; check that case against the installed version.

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
