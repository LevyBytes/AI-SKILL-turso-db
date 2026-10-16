# Timezone interpretation rounding and subseconds

[Context index](date-and-time-function-reference.md) · [Setup and preceding context](date-and-time-construction.md) · [Previous phase](time-offset-start-and-weekday-modifiers.md) · [Continue](date-arithmetic-age-and-grouping.md)

### Interpretation Modifiers

| Modifier | Description |
| --- | --- |
| `unixepoch` | Read the time value as a Unix timestamp, in seconds since 1970-01-01 |
| `julianday` | Read the time value as a Julian day number |
| `auto` | Detect automatically whether the value is a Unix timestamp or Julian day |

```
SELECT datetime(1718458200, 'unixepoch');
-- '2024-06-15 13:30:00'

SELECT datetime(2460841.5, 'julianday');
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
| `ceiling` | Resolve month/year ambiguity by choosing the later date, retaining overflow days |
| `floor` | For month addition with day overflow, choose the final day of the target month |

 These modifiers control month arithmetic when months do not have the same day counts.

```
-- Without floor/ceiling, month overflow rolls forward
SELECT date('2025-01-31', '+1 months');
-- '2025-03-03'

-- With floor, clamp to last day of target month
SELECT date('2025-01-31', '+1 months', 'floor');
-- '2025-02-28'

-- With ceiling, retain the later overflow date
SELECT date('2025-01-31', '+1 months', 'ceiling');
-- '2025-03-03'

```

### Subsec Modifier

 With the `subsec` modifier, `unixepoch()` returns a REAL containing fractional seconds rather than truncating to INTEGER.

```
SELECT unixepoch('2025-06-15 14:30:00.456', 'subsec');
-- 1749997800.456

```

### Practical Examples

### Get the Current Date and Time

```
SELECT date('now');                    -- Current UTC date
SELECT time('now');                    -- Current UTC time
SELECT datetime('now');                -- Current UTC date and time
SELECT datetime('now', 'localtime');   -- Current local date and time

```
