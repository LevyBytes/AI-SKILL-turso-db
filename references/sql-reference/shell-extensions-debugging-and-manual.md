# Shell extensions debugging and manual

[Context index](shell-and-compatibility-workflows.md) · [Setup and preceding context](shell-database-navigation-commands.md) · [Previous phase](shell-import-clone-and-script-loading.md)

### .load

 Load an extension library.

```
.load <PATH>

```

```
tursodb> .load ./liblimbo_regexp

```

 Turso can load only Turso-native extensions. SQLite `.so`/`.dll` loadable extensions do not work. Available extensions are listed in the [Extensions](https://docs.turso.tech/sql-reference/extensions) documentation.

### Debugging

### .dbtotxt

 Show raw database pages in hex format. Helpful when investigating storage-level problems.

```
.dbtotxt [--page PAGE_NO]

```

```
tursodb> .dbtotxt --page 1
| size 8192 pagesize 4096 filename :memory:
| page 1 offset 0
|      0: 53 51 4c 69 74 65 20 66 6f 72 6d 61 74 20 33 00   SQLite format 3.
|     16: 10 00 02 02 00 40 20 20 00 00 00 01 00 00 00 02   .....@  ........
...

```

### .dbconfig

 Show or change database configuration flags. In current Turso builds, this is a no-op.

```
.dbconfig [CONFIG] [on|off]

```

### Documentation

### .manual

 Show Turso built-in manual pages. With no argument, it lists all available manuals.

```
.manual [PAGE]

```

 Aliases: `.man`.

```
tursodb> .manual
# Turso Manual Pages

Available manuals:
  custom-types    Custom Types for STRICT Tables
  cdc             Change Data Capture
  encryption      At-Rest Database Encryption
  vector          Vector Search
  materialized-views  Live Materialized Views
  mcp             Model Context Protocol server

tursodb> .manual vector

```
