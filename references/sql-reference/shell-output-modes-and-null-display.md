# Shell output modes and null display

[Context index](shell-and-compatibility-workflows.md) · [Setup and preceding context](shell-database-navigation-commands.md) · [Previous phase](shell-schema-inspection-commands.md) · [Continue](shell-output-files-echo-and-timing.md)

### .mode

 Set the output display mode.

```
.mode <MODE>

```

| Mode | Description |
| --- | --- |
| `pretty` | Table with borders (default) |
| `list` | Pipe-delimited values |
| `line` | Column names are included, with one column shown on each line |

```
tursodb> .mode list
tursodb> SELECT 1 AS a, 2 AS b;
1|2

tursodb> .mode line
tursodb> SELECT 1 AS a, 2 AS b;
a = 1
b = 2

tursodb> .mode pretty
tursodb> SELECT 1 AS a, 2 AS b;
┌───┬───┐
│ a │ b │
├───┼───┤
│ 1 │ 2 │
└───┴───┘

```

### .headers

 Switch column headers on or off for `list` mode.

```
.headers <on|off>

```

```
tursodb> .mode list
tursodb> .headers on
tursodb> SELECT 1 AS x, 2 AS y;
x|y
1|2

tursodb> .headers off
tursodb> SELECT 1 AS x, 2 AS y;
1|2

```

### .nullvalue

 Choose the text shown for NULL values in `list` mode.

```
.nullvalue <STRING>

```

```
tursodb> .mode list
tursodb> .nullvalue [NULL]
tursodb> SELECT 1 AS a, NULL AS b;
1|[NULL]

```
