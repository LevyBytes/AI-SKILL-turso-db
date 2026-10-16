# Shell output files echo and timing

[Context index](shell-and-compatibility-workflows.md) · [Setup and preceding context](shell-database-navigation-commands.md) · [Previous phase](shell-output-modes-and-null-display.md) · [Continue](shell-statistics-opcodes-and-dumps.md)

### .output

 Send query output to a file. Use no argument, or pass `stdout`, to return output to the terminal.

```
.output [PATH]

```

```
tursodb> .output results.txt
tursodb> SELECT * FROM employees;
tursodb> .output
tursodb> -- Output is back to the terminal

```

### .echo

 Switch echo mode. When enabled, every SQL statement is printed before it runs.

```
.echo <on|off>

```

```
tursodb> .echo on
tursodb> SELECT 42;
SELECT 42;
┌────┐
│ 42 │
├────┤
│ 42 │
└────┘

```

### .show

 Display current shell settings.

```
tursodb> .show
Settings:
Output mode: pretty
DB: mydata.db
Output: STDOUT
Null value:
CWD: /home/user
Echo: off
Headers: off

```

### Performance & Diagnostics

### .timer

 Switch query timing. When enabled, execution time and I/O statistics appear after each query.

```
.timer <on|off>

```

```
tursodb> .timer on
tursodb> SELECT 42;
42
Command stats:
----------------------------
total: 442 us (this includes parsing/coloring of cli app)

query execution stats:
----------------------------
Execution: avg=4 us, total=8 us
I/O: No samples available

```
