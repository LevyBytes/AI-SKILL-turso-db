# Date and time construction

[Context index](date-and-time-function-reference.md) · [Continue](datetime-and-julian-day-functions.md)

### Date and Time Functions

 Turso includes a full date and time function set that matches SQLite behavior. The functions can take time values written in multiple forms, process optional modifiers, and produce TEXT, REAL, or INTEGER output based on which function is called. Every date and time function uses the [proleptic Gregorian calendar](https://en.wikipedia.org/wiki/Proleptic_Gregorian_calendar) and treats values as UTC unless `localtime` is specified.

### Functions

### date()

 Produces the date as TEXT using the `YYYY-MM-DD` format.

```
date(time-value, modifier, modifier, ...)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `time-value` | TEXT, REAL, or INTEGER | Any supported format for a time value |
| `modifier` | TEXT | Optional modifiers, processed from left to right |

 **Returns:** TEXT formatted as `YYYY-MM-DD`, or NULL when an argument is not valid.

```
SELECT date('now');
-- '2025-06-15'

SELECT date('2025-06-15 14:30:00');
-- '2025-06-15'

SELECT date('2025-06-15', '+1 month');
-- '2025-07-15'

SELECT date('2025-06-15', 'start of year');
-- '2025-01-01'

```

---

### time()

 Produces the time as TEXT using the `HH:MM:SS` format.

```
time(time-value, modifier, modifier, ...)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `time-value` | TEXT, REAL, or INTEGER | Any supported format for a time value |
| `modifier` | TEXT | Optional modifiers, processed from left to right |

 **Returns:** TEXT formatted as `HH:MM:SS`, or NULL when an argument is not valid.

```
SELECT time('now');
-- '14:30:00'

SELECT time('2025-06-15 14:30:00');
-- '14:30:00'

SELECT time('14:30:00', '+2 hours');
-- '16:30:00'

SELECT time('14:30:00', '+90 minutes');
-- '16:00:00'

```

---
