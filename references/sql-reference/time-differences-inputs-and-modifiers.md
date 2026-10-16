# Time differences inputs and modifiers

[Context index](date-and-time-function-reference.md) · [Setup and preceding context](date-and-time-construction.md) · [Previous phase](strftime-formatting-and-format-codes.md) · [Continue](time-offset-start-and-weekday-modifiers.md)

### timediff()

 Produces the interval between two time values as a TEXT string formatted as `(+|-)YYYY-MM-DD HH:MM:SS.SSS`. The value is the amount to add to `time2` to get `time1`.

```
timediff(time1, time2)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `time1` | TEXT, REAL, or INTEGER | The later time value |
| `time2` | TEXT, REAL, or INTEGER | The earlier time value |

 **Returns:** TEXT formatted as `(+|-)YYYY-MM-DD HH:MM:SS.SSS`, or NULL when either argument is not valid.

```
SELECT timediff('2025-06-15', '2025-06-10');
-- '+0000-00-05 00:00:00.000'

SELECT timediff('2025-01-01', '2024-01-01');
-- '+0001-00-00 00:00:00.000'

SELECT timediff('2025-06-15 14:30:00', '2025-06-15 10:00:00');
-- '+0000-00-00 04:30:00.000'

SELECT timediff('2024-01-01', '2025-06-15');
-- '-0001-05-14 00:00:00.000'

```

### Time Value Formats

 Date and time functions accept time values in these formats.

| Format | Example | Description |
| --- | --- | --- |
| `YYYY-MM-DD` | `'2025-06-15'` | Date only (time defaults to 00:00:00) |
| `YYYY-MM-DD HH:MM` | `'2025-06-15 14:30'` | Date and time, with seconds defaulting to 00 |
| `YYYY-MM-DD HH:MM:SS` | `'2025-06-15 14:30:00'` | Date and time with seconds |
| `YYYY-MM-DD HH:MM:SS.SSS` | `'2025-06-15 14:30:00.123'` | Date and time including fractional seconds |
| `YYYY-MM-DDTHH:MM` | `'2025-06-15T14:30'` | ISO 8601 with `T` separator |
| `YYYY-MM-DDTHH:MM:SS` | `'2025-06-15T14:30:00'` | ISO 8601 with `T` separator |
| `YYYY-MM-DDTHH:MM:SS.SSS` | `'2025-06-15T14:30:00.123'` | ISO 8601 with `T` separator |
| `HH:MM` | `'14:30'` | Time only (date defaults to 2000-01-01) |
| `HH:MM:SS` | `'14:30:00'` | Time only with seconds |
| `HH:MM:SS.SSS` | `'14:30:00.123'` | Time only with fractional seconds |
| `now` | `'now'` | Current UTC date and time |
| Julian day number | `2460841.5` | REAL value used as a Julian day |
| Unix timestamp | `1718458200` | INTEGER, requires `'unixepoch'` modifier |

 If the time value is a Unix timestamp, add the `'unixepoch'` modifier so the function reads the number as seconds since 1970-01-01 instead of as a Julian day number.

```
-- Unix timestamp requires the 'unixepoch' modifier
SELECT datetime(1718458200, 'unixepoch');
-- '2024-06-15 13:30:00'

-- Without 'unixepoch', the number is treated as a Julian day
SELECT datetime(1718458200);
-- NULL (or a very distant date)

-- 'now' returns the current UTC time
SELECT datetime('now');
-- '2025-06-15 14:30:00'

```

### Modifiers

 Modifiers change the time value. When more than one is supplied, they run from left to right. A function returns NULL if any modifier is not valid.
