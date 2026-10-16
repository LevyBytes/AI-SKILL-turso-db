# Scalar function category reference

[Context index](scalar-function-reference.md) · [Continue](scalar-hints-system-and-basic-functions.md)

### Scalar Functions

 Scalar functions take one or more inputs and produce one value. Use them anywhere expressions are allowed, including SELECT column lists, WHERE filters, ORDER BY and GROUP BY clauses, HAVING predicates, CHECK constraints, and DEFAULT expressions.

### Function Reference

### Math Functions

| Function | Description |
| --- | --- |
| `abs(X)` | Computes X without its sign. Result type is INTEGER when X is INTEGER, REAL when X is REAL, and NULL when X is NULL. |
| `max(X, Y, ...)` | Chooses the argument whose value is highest |
| `min(X, Y, ...)` | Chooses the argument whose value is lowest |
| `random()` | Produces a signed 64-bit integer selected pseudo-randomly |
| `round(X)` | Rounds X to the closest whole number |
| `round(X, Y)` | Rounds X using Y digits after the decimal point |
| `sign(X)` | Maps X to -1, 0, or 1 depending on whether it is negative, zero, or positive |

### String Functions

| Function | Description |
| --- | --- |
| `char(X1, X2, ..., XN)` | Builds a string from characters whose Unicode code points are X1 through XN |
| `concat(X, ...)` | Joins every argument after converting to strings, while ignoring NULL inputs |
| `concat_ws(SEP, X, ...)` | Joins arguments separated by SEP, leaving out any NULL input |
| `format(FORMAT, ...)` | Builds text from printf-style format specifiers |
| `hex(X)` | Gives X as uppercase hexadecimal text |
| `instr(X, Y)` | Reports the 1-based location where Y first appears in X, or 0 when absent |
| `length(X)` | Reports character count for strings, and byte count for blobs |
| `lower(X)` | Creates a lowercase version of string X |
| `ltrim(X)` | Removes leading whitespace from X |
| `ltrim(X, Y)` | Strips from the front of X any characters present in Y |
| `octet_length(X)` | Reports X's byte length |
| `printf(FORMAT, ...)` | Alias for `format()`. Produces a formatted string |
| `quote(X)` | Produces the SQL literal form for X |
| `replace(X, Y, Z)` | Copies X while substituting Z for each occurrence of Y |
| `rtrim(X)` | Removes trailing whitespace from X |
| `rtrim(X, Y)` | Strips from the end of X any characters present in Y |
| `soundex(X)` | Produces the Soundex code for string X |
| `substr(X, Y)` | Extracts part of X beginning at 1-based position Y |
| `substr(X, Y, Z)` | Extracts Z characters from X beginning at position Y |
| `substring(X, Y)` | Alias for `substr(X, Y)` |
| `substring(X, Y, Z)` | Alias for `substr(X, Y, Z)` |
| `trim(X)` | Strips whitespace from both ends of X |
| `trim(X, Y)` | Strips from both ends of X any characters listed in Y |
| `unhex(X)` | Parses hexadecimal string X into a blob. If X includes non-hex characters, result is NULL |
| `unhex(X, Y)` | Same as `unhex(X)`, except characters listed in Y are ignored while reading X |
| `unicode(X)` | Reports the Unicode code point for string X's first character |
| `upper(X)` | Creates an uppercase version of string X |
| `zeroblob(N)` | Creates a blob containing N zero bytes |

### Conditional Functions

| Function | Description |
| --- | --- |
| `coalesce(X, Y, ...)` | Chooses the first argument that is not NULL. If none exists, result is NULL |
| `ifnull(X, Y)` | Yields X when X is not NULL; otherwise yields Y. Same result as `coalesce(X, Y)` |
| `iif(X, Y, Z)` | Yields Y when X evaluates true (non-zero); otherwise yields Z |
| `if(X, Y, Z)` | Alias for `iif(X, Y, Z)` |
| `nullif(X, Y)` | Yields NULL when X and Y are equal; otherwise yields X |

### Type Functions

| Function | Description |
| --- | --- |
| `typeof(X)` | Reports X's type as one of these strings: `"null"`, `"integer"`, `"real"`, `"text"`, or `"blob"` |

### Pattern Matching Functions

| Function | Description |
| --- | --- |
| `glob(X, Y)` | Yields 1 when string Y fits glob pattern X; otherwise yields 0. Matching is case-sensitive |
| `like(X, Y)` | Yields 1 when string Y fits LIKE pattern X; otherwise yields 0. ASCII matching ignores case |
| `like(X, Y, Z)` | Same behavior as `like(X, Y)`, with Z supplying the escape character |
