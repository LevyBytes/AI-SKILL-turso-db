# Strftime formatting and format codes

[Context index](date-and-time-function-reference.md) · [Setup and preceding context](date-and-time-construction.md) · [Previous phase](unix-timestamp-conversion.md) · [Continue](time-differences-inputs-and-modifiers.md)

### strftime()

 Produces a date/time string formatted according to the supplied format string.

```
strftime(format, time-value, modifier, modifier, ...)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `format` | TEXT | A format string that includes substitution codes |
| `time-value` | TEXT, REAL, or INTEGER | Any supported format for a time value |
| `modifier` | TEXT | Optional modifiers, processed from left to right |

 **Returns:** TEXT where format codes have been substituted with date/time components, or NULL when an argument is not valid. The other date/time functions can also be written with `strftime` calls:

| Function | Equivalent strftime |
| --- | --- |
| `date(...)` | `strftime('%Y-%m-%d', ...)` |
| `time(...)` | `strftime('%H:%M:%S', ...)` |
| `datetime(...)` | `strftime('%Y-%m-%d %H:%M:%S', ...)` |
| `julianday(...)` | `strftime('%J', ...)` (as TEXT) |
| `unixepoch(...)` | `strftime('%s', ...)` (as TEXT) |

```
SELECT strftime('%Y', 'now');
-- '2025'

SELECT strftime('%m/%d/%Y', '2025-06-15');
-- '06/15/2025'

SELECT strftime('%H:%M', '2025-06-15 14:30:00');
-- '14:30'

SELECT strftime('%Y-%m-%d %H:%M:%f', '2025-06-15 14:30:00.123');
-- '2025-06-15 14:30:00.123'

SELECT strftime('%s', '2025-06-15 14:30:00');
-- '1749997800'

SELECT strftime('%W', '2025-06-15');
-- '23' (week of year)

```

#### strftime Format Codes

| Code | Description | Example |
| --- | --- | --- |
| `%d` | Day of month (01-31) | `15` |
| `%e` | Day of month, padded with a leading space ( 1-31) | `15` |
| `%f` | Fractional seconds (SS.SSS) | `00.123` |
| `%F` | ISO 8601 date (YYYY-MM-DD) | `2025-06-15` |
| `%H` | Hour (00-23) | `14` |
| `%I` | Hour (01-12) | `02` |
| `%j` | Day of year (001-366) | `166` |
| `%J` | Julian day number | `2460841.5` |
| `%k` | Hour with leading space ( 0-23) | `14` |
| `%l` | Hour with leading space ( 1-12) | ` 2` |
| `%m` | Month (01-12) | `06` |
| `%M` | Minute (00-59) | `30` |
| `%p` | AM or PM | `PM` |
| `%P` | am or pm | `pm` |
| `%R` | Time as HH:MM | `14:30` |
| `%s` | Seconds since Unix epoch | `1749997800` |
| `%S` | Seconds (00-59) | `00` |
| `%T` | Time as HH:MM:SS | `14:30:00` |
| `%u` | Day of week (1=Monday, 7=Sunday) | `7` |
| `%w` | Day of week (0=Sunday, 6=Saturday) | `0` |
| `%W` | Week of year (00-53) | `23` |
| `%Y` | Year (0000-9999) | `2025` |
| `%%` | Literal `%` character | `%` |

---
