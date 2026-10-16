# CLI experimental and other flags

[Context index](shell-and-compatibility-workflows.md) · [Setup and preceding context](cli-arguments-and-output-options.md) · [Previous phase](cli-filesystems-readonly-and-server-modes.md)

### Experimental Feature Flags

 These flags turn on work-in-progress features. Future releases may revise or remove them. Implementations can be incomplete or carry known limits, so reserve them for testing and development.

| Flag | Description |
| --- | --- |
| `--experimental-views` | Enable views (`CREATE VIEW` / `DROP VIEW`) |
| `--experimental-custom-types` | Enable custom types (`CREATE TYPE` / `DROP TYPE`) |
| `--experimental-encryption` | Enable at-rest database encryption |
| `--experimental-index-method` | Turn on custom index methods. Required for FTS and Sparse Vector indexes |
| `--experimental-autovacuum` | Enable automatic database vacuuming |
| `--experimental-vacuum` | Enable in-place `VACUUM` |
| `--experimental-triggers` | Enable triggers (`CREATE TRIGGER` / `DROP TRIGGER`) |
| `--experimental-attach` | Enable `ATTACH DATABASE` / `DETACH DATABASE` |

```
# Enable views, triggers, and in-place VACUUM
tursodb --experimental-views --experimental-triggers --experimental-vacuum mydata.db

```

### Other Flags

| Flag | Description |
| --- | --- |
| `--unsafe-testing` | Enable unsafe testing features (e.g., `sqlite_dbpage` writes) |
| `-h, --help` | Print usage help |
| `-V, --version` | Print version information |
