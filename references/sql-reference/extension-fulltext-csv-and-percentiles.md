# Extension fulltext csv and percentiles

[Context index](expressions-and-extensions-workflows.md) · [Setup and preceding context](extension-loading-uuid-and-regexp.md) · [Previous phase](extension-time-functions-and-durations.md) · [Continue](extension-generated-number-series.md)

### Quick Reference

```
-- Create an FTS index
CREATE INDEX idx ON articles USING fts (title, body);

-- Search
SELECT title, fts_score(title, body, 'database search') AS score
FROM articles
WHERE fts_match(title, body, 'database search')
ORDER BY score DESC;

-- Highlighted results
SELECT fts_highlight(title, '<b>', '</b>', 'database') AS title
FROM articles
WHERE fts_match(title, body, 'database');

```

### CSV Extension

 The CSV extension supplies a virtual table for reading data from CSV files.

### Usage

```
CREATE VIRTUAL TABLE temp.csv_data USING csv(
    filename='/path/to/data.csv',
    header=yes
);

SELECT * FROM csv_data;

```

| Parameter | Description |
| --- | --- |
| `filename` | Path to the CSV file |
| `header` | `yes` when the first row supplies column names |

### Percentile Extension

 Statistical aggregate functions that calculate percentiles.

### Functions

| Function | Parameters | Return Type | Description |
| --- | --- | --- | --- |
| `median(X)` | column | REAL | Median value (50th percentile) |
| `percentile(Y, P)` | column, REAL | REAL | P-th percentile for Y, where P is between 0 and 100 |
| `percentile_cont(Y, P)` | column, REAL | REAL | Continuous percentile (interpolated) |
| `percentile_disc(Y, P)` | column, REAL | REAL | Discrete percentile (nearest value) |

### Examples

```
CREATE TABLE scores (value REAL);
INSERT INTO scores VALUES (10), (20), (30), (40), (50);

SELECT median(value) FROM scores;
-- 30.0

SELECT percentile(value, 75) FROM scores;
-- 40.0

SELECT percentile_cont(value, 0.25) FROM scores;
-- 20.0

```
