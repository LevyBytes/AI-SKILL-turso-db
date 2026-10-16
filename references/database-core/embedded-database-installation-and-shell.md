# Embedded database installation and shell

[Context index](embedded-database-mvcc-and-encryption.md) · [Continue](embedded-database-sql-and-experimental-features.md)

## Experimental Features

### Step 1: Install

-  macOS / Linux
-  Windows

```
curl --proto '=https' --tlsv1.2 -LsSf https://github.com/tursodatabase/turso/releases/latest/download/turso_cli-installer.sh | sh

```

Execute this command from PowerShell:

```
irm https://github.com/tursodatabase/turso/releases/latest/download/turso_cli-installer.ps1 | iex

```

As another option, [download the Windows executable](https://github.com/tursodatabase/turso/releases/latest) and place it on your PATH.

### Step 2: Launch

Then launch the interactive shell:

```
tursodb

```

That starts the Turso interactive shell, where SQL statements can be run:

```
Turso
Enter ".help" for usage hints.
Connected to a transient in-memory database.
Use ".open FILENAME" to reopen on a persistent database
turso>

```
