# Group token creation and invalidation

[Context index](installation-organization-and-account-commands.md) · [Setup and preceding context](api-token-list-and-scopes.md) · [Previous phase](group-migration-creation-and-destruction.md) · [Continue](group-transfer-unarchive-and-updates.md)

## `turso group tokens create`

Create a token for connecting to any database within the group with this command:

```
turso group tokens create <group-name> [flags]

```

### Flags

| Flag | Description |
| --- | --- |
| `-e`, `--expiration` | Token expiration; use `never` or a day-based value such as `7d` . |
| `-r`, `--read-only` | Limit this token to read only access. |

### Examples

 These examples show common patterns for using the `group tokens create` command.

### Create a token with read only access

 Create a read only access token with this command:

```
turso group tokens create <group-name> --read-only

```

### Create a token with a specific expiration time

 Create a token that expires at a specific time by using this command:

```
turso group tokens create <group-name> --expiration 7d

```

## `turso group tokens invalidate`

Run this command to invalidate every token for a group:

```
turso group tokens invalidate <group-name> [flags]

```

### Flags

| Flag | Description |
| --- | --- |
| `-y`, `--yes` | Confirms that all current group tokens should be invalidated. |
