# Expression literals and boolean values

[Context index](expressions-and-extensions-workflows.md) · [Continue](expression-arithmetic-comparison-and-array-operators.md)

## Expressions

 An expression combines values, operators, and functions, and Turso evaluates that combination into a result. You can use expressions in SQL contexts such as SELECT column lists, WHERE filters, ORDER BY, GROUP BY, HAVING, CHECK constraints, and DEFAULT clauses.

### Literals

### Numeric Literals

```
SELECT 42;           -- integer literal
SELECT -17;          -- negative integer
SELECT 3.14;         -- real (floating-point) literal
SELECT 2.5e10;       -- scientific notation
SELECT 0xFF;         -- hexadecimal integer (255)

```

### String Literals

 Use single quotes around string literals. Inside a string, represent one literal single quote by writing two single quotes in a row:

```
SELECT 'hello world';        -- text literal
SELECT 'it''s a test';       -- embedded single quote
SELECT '';                   -- empty string

```

### Blob Literals

 Blob literals use hexadecimal text with a leading `x` or `X`:

```
SELECT x'CAFEBABE';          -- blob literal
SELECT X'48454C4C4F';        -- blob literal (case insensitive prefix)

```

### NULL Literal

```
SELECT NULL;                 -- null value

```

### Boolean Literals

 Turso has no dedicated boolean storage type. Represent false with integer `0` and true with integer `1`:

```
SELECT 1;    -- true
SELECT 0;    -- false

```

### Operators
