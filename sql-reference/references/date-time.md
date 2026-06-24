# Date Time

> Sql Reference reference. Original prose; identifiers preserved verbatim.

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
-- '2024-06-15 14:30:00'

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
-- 2460811.5

SELECT julianday('now');
-- 2460811.104166...

-- Compute the number of days between two dates
SELECT julianday('2025-12-25') - julianday('2025-06-15');
-- 193.0

```

---

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
-- 1750001400

SELECT unixepoch('2025-06-15 14:30:00.123', 'subsec');
-- 1750001400.123

```

---

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
-- '1750001400'

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
| `%J` | Julian day number | `2460811.5` |
| `%k` | Hour with leading space ( 0-23) | `14` |
| `%l` | Hour with leading space ( 1-12) | ` 2` |
| `%m` | Month (01-12) | `06` |
| `%M` | Minute (00-59) | `30` |
| `%p` | AM or PM | `PM` |
| `%P` | am or pm | `pm` |
| `%R` | Time as HH:MM | `14:30` |
| `%s` | Seconds since Unix epoch | `1750001400` |
| `%S` | Seconds (00-59) | `00` |
| `%T` | Time as HH:MM:SS | `14:30:00` |
| `%u` | Day of week (1=Monday, 7=Sunday) | `7` |
| `%w` | Day of week (0=Sunday, 6=Saturday) | `0` |
| `%W` | Week of year (00-53) | `23` |
| `%Y` | Year (0000-9999) | `2025` |
| `%%` | Literal `%` character | `%` |

---

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
| Julian day number | `2460811.5` | REAL value used as a Julian day |
| Unix timestamp | `1718458200` | INTEGER, requires `'unixepoch'` modifier |

 If the time value is a Unix timestamp, add the `'unixepoch'` modifier so the function reads the number as seconds since 1970-01-01 instead of as a Julian day number.

```
-- Unix timestamp requires the 'unixepoch' modifier
SELECT datetime(1718458200, 'unixepoch');
-- '2024-06-15 14:30:00'

-- Without 'unixepoch', the number is treated as a Julian day
SELECT datetime(1718458200);
-- NULL (or a very distant date)

-- 'now' returns the current UTC time
SELECT datetime('now');
-- '2025-06-15 14:30:00'

```

### Modifiers

 Modifiers change the time value. When more than one is supplied, they run from left to right. A function returns NULL if any modifier is not valid.

### Offset Modifiers

 Offset modifiers move the time value forward or backward by the requested amount.
| Modifier | Description | Example |
| --- | --- | --- |
| `NNN days` | Add NNN days | `'+7 days'`, `'-1 days'` |
| `NNN hours` | Add NNN hours | `'+6 hours'` |
| `NNN minutes` | Add NNN minutes | `'+30 minutes'` |
| `NNN seconds` | Add NNN seconds | `'+90 seconds'` |
| `NNN.NNNN seconds` | Add fractional seconds | `'+0.5 seconds'` |
| `NNN months` | Add NNN months | `'+1 months'` |
| `NNN years` | Add NNN years | `'+1 years'` |

 NNN may be a negative or positive integer, with real numbers allowed for seconds. Positive values may omit the `+` sign.

```
SELECT date('2025-06-15', '+7 days');
-- '2025-06-22'

SELECT datetime('2025-06-15 14:30:00', '-6 hours');
-- '2025-06-15 08:30:00'

SELECT date('2025-01-31', '+1 months');
-- '2025-03-03' (Jan 31 + 1 month = Mar 3)

```

### Time Offset Modifier

 A time offset written as `+HH:MM` or `-HH:MM` shifts the value by the given hours and minutes.

```
SELECT time('14:30:00', '+05:30');
-- '20:00:00'

SELECT datetime('2025-06-15 14:30:00', '-08:00');
-- '2025-06-15 06:30:00'

```

### Start-of Modifiers

 These modifiers move the time value back to the beginning of a period.
| Modifier | Description |
| --- | --- |
| `start of day` | Sets time to 00:00:00, keeps date |
| `start of month` | Moves to the month's first day and sets the time to 00:00:00 |
| `start of year` | Moves to January 1 of the year and sets the time to 00:00:00 |

```
SELECT datetime('2025-06-15 14:30:00', 'start of day');
-- '2025-06-15 00:00:00'

SELECT date('2025-06-15', 'start of month');
-- '2025-06-01'

SELECT date('2025-06-15', 'start of year');
-- '2025-01-01'

```

### Weekday Modifier

 The `weekday N` modifier moves the date forward to the next matching weekday, using 0 = Sunday, 1 = Monday, â€¦, 6 = Saturday. When the date is already on that weekday, no change is made.

```
-- Next Sunday (weekday 0)
SELECT date('2025-06-15', 'weekday 0');
-- '2025-06-15' (June 15, 2025 is a Sunday)

-- Next Monday
SELECT date('2025-06-15', 'weekday 1');
-- '2025-06-16'

-- Next Friday
SELECT date('2025-06-15', 'weekday 5');
-- '2025-06-20'

```

### Interpretation Modifiers

| Modifier | Description |
| --- | --- |
| `unixepoch` | Read the time value as a Unix timestamp, in seconds since 1970-01-01 |
| `julianday` | Read the time value as a Julian day number |
| `auto` | Detect automatically whether the value is a Unix timestamp or Julian day |

```
SELECT datetime(1718458200, 'unixepoch');
-- '2024-06-15 14:30:00'

SELECT datetime(2460811.5, 'julianday');
-- '2025-06-15 00:00:00'

SELECT datetime(0, 'unixepoch');
-- '1970-01-01 00:00:00'

```

### Timezone Modifiers

| Modifier | Description |
| --- | --- |
| `localtime` | Convert UTC to local time |
| `utc` | Convert local time to UTC |

```
SELECT datetime('2025-06-15 14:30:00', 'localtime');
-- '2025-06-15 07:30:00' (example: UTC-7)

SELECT time('now', 'localtime');
-- Local current time

```

### Rounding Modifiers

| Modifier | Description |
| --- | --- |
| `ceiling` | For month addition with day overflow, move to the first day of the following month |
| `floor` | For month addition with day overflow, choose the final day of the target month |

 These modifiers control month arithmetic when months do not have the same day counts.

```
-- Without floor/ceiling, month overflow rolls forward
SELECT date('2025-01-31', '+1 months');
-- '2025-03-03'

-- With floor, clamp to last day of target month
SELECT date('2025-01-31', 'floor', '+1 months');
-- '2025-02-28'

-- With ceiling, advance to first of next month
SELECT date('2025-01-31', 'ceiling', '+1 months');
-- '2025-03-01'

```

### Subsec Modifier

 With the `subsec` modifier, `unixepoch()` returns a REAL containing fractional seconds rather than truncating to INTEGER.

```
SELECT unixepoch('2025-06-15 14:30:00.456', 'subsec');
-- 1750001400.456

```

### Practical Examples

### Get the Current Date and Time

```
SELECT date('now');                    -- Current UTC date
SELECT time('now');                    -- Current UTC time
SELECT datetime('now');                -- Current UTC date and time
SELECT datetime('now', 'localtime');   -- Current local date and time

```

### Date Arithmetic

```
-- Tomorrow
SELECT date('now', '+1 day');

-- 90 days from now
SELECT date('now', '+90 days');

-- Last day of the current month
SELECT date('now', 'start of month', '+1 month', '-1 day');

-- First Monday of the current month
SELECT date('now', 'start of month', 'weekday 1');

```

### Age Calculation

```
SELECT
    name,
    birth_date,
    timediff('now', birth_date) AS age_diff
FROM users;

```

### Convert Between Formats

```
-- Unix timestamp to human-readable
SELECT datetime(1718458200, 'unixepoch');

-- Human-readable to Unix timestamp
SELECT unixepoch('2025-06-15 14:30:00');

-- Date to Julian day
SELECT julianday('2025-06-15');

-- Julian day to date
SELECT date(2460811.5);

```

### Group Records by Time Period

```
-- Count orders per month
SELECT
    strftime('%Y-%m', order_date) AS month,
    COUNT(*) AS order_count
FROM orders
GROUP BY strftime('%Y-%m', order_date)
ORDER BY month;

-- Count events per day of week
SELECT
    CASE strftime('%w', event_date)
        WHEN '0' THEN 'Sunday'
        WHEN '1' THEN 'Monday'
        WHEN '2' THEN 'Tuesday'
        WHEN '3' THEN 'Wednesday'
        WHEN '4' THEN 'Thursday'
        WHEN '5' THEN 'Friday'
        WHEN '6' THEN 'Saturday'
    END AS day_name,
    COUNT(*) AS event_count
FROM events
GROUP BY strftime('%w', event_date)
ORDER BY strftime('%w', event_date);

```

### Filter by Date Range

```
-- Records from the last 30 days
SELECT * FROM logs
WHERE timestamp >= datetime('now', '-30 days');

-- Records from a specific month
SELECT * FROM orders
WHERE order_date >= '2025-06-01'
  AND order_date < '2025-07-01';

-- Records from this year
SELECT * FROM sales
WHERE date(sale_date) >= date('now', 'start of year');

```

### Compute Elapsed Time

```
-- Days between two dates
SELECT julianday('2025-12-25') - julianday('now') AS days_until_christmas;

-- Seconds between two timestamps
SELECT unixepoch('2025-06-15 18:00:00') - unixepoch('2025-06-15 14:30:00') AS seconds_elapsed;
-- 12600

```

### See Also

- [Expressions](https://docs.turso.tech/sql-reference/expressions) explains how to use functions inside expressions
- [Data Types](https://docs.turso.tech/sql-reference/data-types) covers the TEXT, REAL, and INTEGER storage classes
- [Scalar Functions](https://docs.turso.tech/sql-reference/functions/scalar) lists additional built-in functions
