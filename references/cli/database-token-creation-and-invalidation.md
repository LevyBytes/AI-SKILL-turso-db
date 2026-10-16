# Database token creation and invalidation

[Context index](database-and-group-commands.md) · [Setup and preceding context](api-token-list-and-scopes.md) · [Previous phase](database-details-and-connection-urls.md) · [Continue](local-dumps-and-sqlite-connections.md)

## `turso db tokens create`

Use this command to create a new token for connecting to a single database:

```
turso db tokens create <database-name> [flags]

```

### Flags

| Flag | Description |
| --- | --- |
| `-e`, `--expiration` | Token expiration time; use `never` or a day value such as `7d` . |
| `-r`, `--read-only` | Limit the token to read only access. |

### Examples

 The examples below show common uses of the `db tokens create` command.

### Create a token with read only access

 Create a database token with read only access by running this command:

```
turso db tokens create <database-name> --read-only

```

### Create a token with a specific expiration time

 Create a token that expires at a chosen time with this command:

```
turso db tokens create <database-name> --expiration 7d3h2m1s

```

## `turso db tokens invalidate`

Run this command to invalidate every token for a database:

```
turso db tokens invalidate <database-name> [flags]

```

### Flags

| Flag | Description |
| --- | --- |
| `-y`, `--yes` | Confirms invalidation for all current db tokens. |

 Tokens for the group containing the database are invalidated too. Existing tokens stop working and must be generated again.
