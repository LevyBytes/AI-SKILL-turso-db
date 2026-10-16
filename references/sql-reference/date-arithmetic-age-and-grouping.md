# Date arithmetic age and grouping

[Context index](date-and-time-function-reference.md) · [Setup and preceding context](date-and-time-construction.md) · [Previous phase](timezone-interpretation-rounding-and-subseconds.md) · [Continue](date-range-filtering-and-elapsed-time.md)

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
SELECT date(2460841.5);

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
