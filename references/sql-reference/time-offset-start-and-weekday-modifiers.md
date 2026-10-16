# Time offset start and weekday modifiers

[Context index](date-and-time-function-reference.md) · [Setup and preceding context](date-and-time-construction.md) · [Previous phase](time-differences-inputs-and-modifiers.md) · [Continue](timezone-interpretation-rounding-and-subseconds.md)

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

 The `weekday N` modifier moves the date forward to the next matching weekday, using 0 = Sunday, 1 = Monday, …, 6 = Saturday. When the date is already on that weekday, no change is made.

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
