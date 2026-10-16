# Shell database navigation commands

[Context index](shell-and-compatibility-workflows.md) · [Continue](shell-schema-inspection-commands.md)

## Shell Commands

 Dot commands are interactive-shell commands prefixed by a period (`.`). They can be entered without a trailing semicolon.

```
tursodb> .help

```

### Database & Navigation

### .open

 Open a database file and, if needed, name a VFS backend.

```
.open <PATH> [VFS]

```

```
tursodb> .open mydata.db
tursodb> .open mydata.db memory

```

### .quit

 Leave the shell. Aliases: `.q`, `.qu`, `.qui`.

```
tursodb> .quit

```

### .exit

 Leave the shell, optionally with a return code. Aliases: `.ex`, `.exi`.

```
.exit [CODE]

```

```
tursodb> .exit
tursodb> .exit 1

```

### .cd

 Change the current working directory.

```
.cd <DIRECTORY>

```

```
tursodb> .cd /tmp

```

### Schema Inspection
