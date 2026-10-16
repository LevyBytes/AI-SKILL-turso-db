# Window function limitations

[Context index](vector-and-window-function-reference.md) · [Setup and preceding context](window-syntax-frames-and-partitioning.md) · [Previous phase](window-running-aggregates-and-percentages.md)

### Limitations

 These window function capabilities are not supported in Turso yet:

| Feature | Status |
| --- | --- |
| `row_number()` | Not supported |
| `rank()` | Not supported |
| `dense_rank()` | Not supported |
| `ntile(N)` | Not supported |
| `lag(expr, offset, default)` | Not supported |
| `lead(expr, offset, default)` | Not supported |
| `first_value(expr)` | Not supported |
| `last_value(expr)` | Not supported |
| `nth_value(expr, N)` | Not supported |
| `cume_dist()` | Not supported |
| `percent_rank()` | Not supported |
| Custom frame: `ROWS BETWEEN ...` | Not supported |
| Custom frame: `RANGE BETWEEN ... AND ...` | Not supported |
| Custom frame: `GROUPS BETWEEN ...` | Not supported |
| `EXCLUDE` clause | Not supported |
| `FILTER (WHERE ...)` on window functions | Not supported |

### See Also

- [SELECT](https://docs.turso.tech/sql-reference/statements/select) documents the complete SELECT syntax, including the WINDOW clause
- [Aggregate Functions](https://docs.turso.tech/sql-reference/functions/aggregate) provides the aggregate function reference
- [Expressions](https://docs.turso.tech/sql-reference/expressions) covers using window function results inside expressions
