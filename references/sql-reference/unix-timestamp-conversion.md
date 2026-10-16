# Unix timestamp conversion

[Context index](date-and-time-function-reference.md) · [Setup and preceding context](date-and-time-construction.md) · [Previous phase](datetime-and-julian-day-functions.md) · [Continue](strftime-formatting-and-format-codes.md)

### unixepoch()

 Produces the Unix timestamp as an INTEGER. A Unix timestamp counts seconds from `1970-01-01 00:00:00 UTC`.

```
unixepoch(time-value, modifier, modifier, ...)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `time-value` | TEXT, REAL, or INTEGER | Any supported format for a time value |
| `modifier` | TEXT | Optional modifiers, processed from left to right |

 **Returns:** INTEGER for seconds since the Unix epoch, or NULL when an argument is not valid. With the `subsec` modifier, the result is REAL and includes fractional seconds.

```
SELECT unixepoch('now');
-- 1718458200

SELECT unixepoch('2025-06-15 14:30:00');
-- 1749997800

SELECT unixepoch('2025-06-15 14:30:00.123', 'subsec');
-- 1749997800.123

```

---
