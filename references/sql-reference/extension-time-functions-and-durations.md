# Extension time functions and durations

[Context index](expressions-and-extensions-workflows.md) · [Setup and preceding context](extension-loading-uuid-and-regexp.md) · [Previous phase](extension-regexp-and-vector-examples.md) · [Continue](extension-fulltext-csv-and-percentiles.md)

### Key Functions

| Function | Parameters | Return Type | Description |
| --- | --- | --- | --- |
| `time_now()` | none | INTEGER | Current time as Unix nanoseconds |
| `time_date(y,m,d,...)` | INTEGER… | INTEGER | Create time from components |
| `time_unix(sec)` | INTEGER | INTEGER | Create time from Unix seconds |
| `time_milli(ms)` | INTEGER | INTEGER | Create time from milliseconds |
| `time_micro(us)` | INTEGER | INTEGER | Create time from microseconds |
| `time_nano(ns)` | INTEGER | INTEGER | Create time from nanoseconds |
| `time_to_unix(t)` | INTEGER | INTEGER | Convert to Unix seconds |
| `time_to_milli(t)` | INTEGER | INTEGER | Convert to milliseconds |
| `time_to_micro(t)` | INTEGER | INTEGER | Convert to microseconds |
| `time_to_nano(t)` | INTEGER | INTEGER | Convert to nanoseconds |
| `time_get_year(t)` | INTEGER | INTEGER | Extract year |
| `time_get_month(t)` | INTEGER | INTEGER | Extract month (1-12) |
| `time_get_day(t)` | INTEGER | INTEGER | Extract day (1-31) |
| `time_get_hour(t)` | INTEGER | INTEGER | Extract hour (0-23) |
| `time_get_minute(t)` | INTEGER | INTEGER | Extract minute (0-59) |
| `time_get_second(t)` | INTEGER | INTEGER | Extract second (0-59) |
| `time_get_weekday(t)` | INTEGER | INTEGER | Day of week (0=Sunday) |
| `time_get(t, field)` | INTEGER, TEXT | INTEGER | Extract named field |
| `time_add(t, d)` | INTEGER, INTEGER | INTEGER | Add duration |
| `time_add_date(t, y, m, d)` | INTEGER… | INTEGER | Add years/months/days |
| `time_sub(t, u)` | INTEGER, INTEGER | INTEGER | Difference between times |
| `time_since(t)` | INTEGER | INTEGER | Duration since time |
| `time_until(t)` | INTEGER | INTEGER | Duration until time |
| `time_trunc(t, field)` | INTEGER, TEXT | INTEGER | Truncate to field |
| `time_after(t, u)` | INTEGER, INTEGER | INTEGER | 1 if t is after u |
| `time_before(t, u)` | INTEGER, INTEGER | INTEGER | 1 if t is before u |
| `time_compare(t, u)` | INTEGER, INTEGER | INTEGER | -1, 0, or 1 |
| `time_equal(t, u)` | INTEGER, INTEGER | INTEGER | 1 if equal |
| `time_fmt_iso(t)` | INTEGER | TEXT | Format as ISO 8601 |
| `time_fmt_datetime(t)` | INTEGER | TEXT | Format as datetime |
| `time_fmt_date(t)` | INTEGER | TEXT | Format as date |
| `time_fmt_time(t)` | INTEGER | TEXT | Format as time |
| `time_parse(s)` | TEXT | INTEGER | Parse time string |

#### Duration Constants

| Function | Value |
| --- | --- |
| `dur_ns()` | 1 nanosecond |
| `dur_us()` | 1 microsecond |
| `dur_ms()` | 1 millisecond |
| `dur_s()` | 1 second |
| `dur_m()` | 1 minute |
| `dur_h()` | 1 hour |

### Example

```
-- Current time formatted
SELECT time_fmt_iso(time_now());
-- '2025-03-15T10:30:00Z'

-- Date arithmetic
SELECT time_fmt_date(time_add_date(time_now(), 0, 1, 0));
-- add 1 month to today

-- Duration since a timestamp
SELECT time_since(time_date(2025, 1, 1, 0, 0, 0)) / dur_h();
-- hours since Jan 1, 2025

```

### Full-Text Search (FTS)

 **Turso Extension**: Full-text search is backed by Tantivy and replaces SQLite's FTS3/FTS4/FTS5. Turso exposes full-text search through the FTS index method. For query syntax, tokenizers, scoring, and the full reference, see [FTS Functions](https://docs.turso.tech/sql-reference/functions/fts).
