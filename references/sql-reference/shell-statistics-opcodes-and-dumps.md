# Shell statistics opcodes and dumps

[Context index](shell-and-compatibility-workflows.md) · [Setup and preceding context](shell-database-navigation-commands.md) · [Previous phase](shell-output-files-echo-and-timing.md) · [Continue](shell-import-clone-and-script-loading.md)

### .stats

 Show database statistics. `on`/`off` controls whether they appear after every query; `--reset` clears counters.

```
.stats [on|off] [--reset]

```

```
tursodb> .stats
Connection Metrics:
  Total statements:     3
  High-water marks:
    Max VM steps:       19
    Max rows read:      1

Aggregate Statistics:
Statement Metrics:
  Row Operations:
    Rows read:        1
    Rows written:     2
  Execution:
    VM steps:         48
    Instructions:     47
  ...

```

### .opcodes

 Display VDBE (Virtual Database Engine) opcodes and their descriptions. Filtering by opcode name is optional.

```
.opcodes [OPCODE]

```

```
tursodb> .opcodes Integer
Integer
-------
The 64-bit integer value P1 is written into register P2. This is different
from SQLite, where this opcode is used for 32-bit integers

```

### .vfslist

 List available Virtual File System modules.

```
tursodb> .vfslist
Available VFS modules:
memory
syscall

```

### Data Import & Export

### .dump

 Emit SQL statements for the full database so it can be recreated.

```
tursodb> .dump
PRAGMA foreign_keys=OFF;
BEGIN TRANSACTION;
CREATE TABLE employees (id INTEGER PRIMARY KEY, name TEXT);
INSERT INTO "employees" VALUES(1,'Alice');
INSERT INTO "employees" VALUES(2,'Bob');
COMMIT;

```

 Pipe `.dump` output into another Turso instance to rebuild the database:

```
tursodb original.db ".dump" | tursodb -q clone.db

```
