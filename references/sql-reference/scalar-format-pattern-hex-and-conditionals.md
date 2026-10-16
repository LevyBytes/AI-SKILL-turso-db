# Scalar format pattern hex and conditionals

[Context index](scalar-function-reference.md) · [Setup and preceding context](scalar-function-category-reference.md) · [Previous phase](scalar-hints-system-and-basic-functions.md) · [Continue](scalar-length-case-trimming-and-null-tests.md)

### format(FORMAT, …) and printf(FORMAT, …)

 Builds a formatted string from printf-style format specifiers. `printf` is another name for `format`.

| Specifier | Description |
| --- | --- |
| `%d` | Signed integer |
| `%f` | Floating-point |
| `%s` | String |
| `%x` | Lowercase hexadecimal integer |
| `%X` | Uppercase hexadecimal integer |
| `%o` | Octal integer |
| `%e` | Scientific notation |
| `%g` | General floating-point (shortest representation) |
| `%%` | Literal percent sign |

```
SELECT format('Hello, %s! You are #%d.', 'Alice', 1);
-- 'Hello, Alice! You are #1.'

SELECT printf('%.2f%%', 99.5);
-- '99.50%'

SELECT format('0x%08X', 255);
-- '0x000000FF'

```

### glob(X, Y)

 Yields 1 when string Y matches glob pattern X, or 0 when it does not. Glob matching is case-sensitive and treats `*` as any character sequence, `?` as one character, and `[...]` as a character class.

```
SELECT glob('*.txt', 'readme.txt');   -- 1
SELECT glob('*.TXT', 'readme.txt');   -- 0 (case-sensitive)
SELECT glob('H?llo', 'Hello');        -- 1

```

 The scalar function `glob(X, Y)` corresponds to the operator form `Y GLOB X`. Its parameters are in the opposite order from the operator syntax.

### hex(X) and unhex(X)

 `hex` emits uppercase hexadecimal text for its input. For text, the UTF-8 bytes are encoded; for blobs, each byte is encoded; numeric inputs are first converted to text and that text is hex-encoded. `unhex` turns a hexadecimal string into a blob. It returns NULL for non-hex input unless a second argument names characters that should be ignored.

```
SELECT hex('ABC');             -- '414243'
SELECT hex(255);               -- '323535' (UTF-8 bytes of 255)
SELECT hex(x'CAFE');           -- 'CAFE'

SELECT unhex('48454C4C4F');    -- x'48454C4C4F' (blob for 'HELLO')
SELECT unhex('48-45-4C', '-'); -- x'48454C' (ignoring dashes)
SELECT unhex('ZZZZ');          -- NULL (invalid hex)

```

### iif(X, Y, Z) and if(X, Y, Z)

 Yields Y when X is true (non-zero and non-NULL); otherwise yields Z. `if` is another name for `iif`.

```
SELECT iif(1 > 0, 'yes', 'no');       -- 'yes'
SELECT iif(NULL, 'yes', 'no');         -- 'no'
SELECT if(10 > 5, 'big', 'small');     -- 'big'

```

### instr(X, Y)

 Reports the 1-based location of the first string Y inside string X. If Y is absent from X, the result is 0. If either argument is NULL, the result is NULL.

```
SELECT instr('Hello World', 'World');   -- 7
SELECT instr('Hello World', 'xyz');     -- 0
SELECT instr('abcabc', 'bc');           -- 2

```
