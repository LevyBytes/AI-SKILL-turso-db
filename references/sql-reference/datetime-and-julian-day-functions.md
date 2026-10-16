# Datetime and julian day functions

[Context index](date-and-time-function-reference.md) · [Setup and preceding context](date-and-time-construction.md) · [Continue](unix-timestamp-conversion.md)

### datetime()

 Produces date plus time as TEXT using the `YYYY-MM-DD HH:MM:SS` format.

```
datetime(time-value, modifier, modifier, ...)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `time-value` | TEXT, REAL, or INTEGER | Any supported format for a time value |
| `modifier` | TEXT | Optional modifiers, processed from left to right |

 **Returns:** TEXT formatted as `YYYY-MM-DD HH:MM:SS`, or NULL when an argument is not valid.

```
SELECT datetime('now');
-- '2025-06-15 14:30:00'

SELECT datetime('2025-06-15', '+1 day', '+6 hours');
-- '2025-06-16 06:00:00'

SELECT datetime(1718458200, 'unixepoch');
-- '2024-06-15 13:30:00'

```

---

### julianday()

 Produces the [Julian day number](https://en.wikipedia.org/wiki/Julian_day) as a REAL value. This value counts days from noon on November 24, 4714 B.C. in the proleptic Gregorian calendar.

```
julianday(time-value, modifier, modifier, ...)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `time-value` | TEXT, REAL, or INTEGER | Any supported format for a time value |
| `modifier` | TEXT | Optional modifiers, processed from left to right |

 **Returns:** REAL for the Julian day number, or NULL when an argument is not valid.

```
SELECT julianday('2025-06-15');
-- 2460841.5

SELECT julianday('now');
-- Example output depends on the current UTC instant.

-- Compute the number of days between two dates
SELECT julianday('2025-12-25') - julianday('2025-06-15');
-- 193.0

```

---
