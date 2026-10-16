# Shell import clone and script loading

[Context index](shell-and-compatibility-workflows.md) · [Setup and preceding context](shell-database-navigation-commands.md) · [Previous phase](shell-statistics-opcodes-and-dumps.md) · [Continue](shell-extensions-debugging-and-manual.md)

### .import

 Load data from a file into a table.

```
.import [--csv] [--skip N] [--verbose] <FILE> <TABLE>

```

| Option | Default | Description |
| --- | --- | --- |
| `--csv` | on | Set comma as the field separator and newline as the record separator |
| `--skip N` | 0 | Omit the first N rows, which is useful for header rows |
| `--verbose` | off | Print progress information during import |

```
tursodb> CREATE TABLE people (name TEXT, age INTEGER);
tursodb> .import --csv --skip 1 data.csv people
tursodb> SELECT * FROM people;
┌─────────┬─────┐
│ name    │ age │
├─────────┼─────┤
│ Alice   │  30 │
├─────────┼─────┤
│ Bob     │  25 │
├─────────┼─────┤
│ Charlie │  35 │
└─────────┴─────┘

```

 For a CSV file named `data.csv`:

```
name,age
Alice,30
Bob,25
Charlie,35

```

### .clone

 Copy the current database into a new file.

```
.clone <OUTPUT_FILE>

```

```
tursodb> .clone backup.db
employees... done

```

### .read

 Run SQL statements read from a file.

```
.read <PATH>

```

```
tursodb> .read setup.sql

```
