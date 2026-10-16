# Database details and connection urls

[Context index](database-and-group-commands.md) · [Setup and preceding context](api-token-list-and-scopes.md) · [Previous phase](database-shell-queries-and-dumps.md) · [Continue](database-token-creation-and-invalidation.md)

## `turso db show`

Retrieve database details such as name, ID, libSQL server version, group, size, and location.

```
turso db show <database-name> [flags]

```

 You can also retrieve the database URLs with this command:

### Flags

| Flag | Description |
| --- | --- |
| `--url` | Display the URL for the database HTTP API. |
| `--http-url` | Display the HTTP URL for the database HTTP API. |
