# Database token expiration and invalidation

[Context index](activerecord-and-authentication-workflows.md)

## Invalidating Tokens

Use the CLI to create tokens for either one database or every database in a group:

```
# Token for a single database
turso db tokens create <database-name>

# Token for all databases in a group
turso group tokens create <group-name>

```

 Token scope, permissions, and expiration can be adjusted:

| Flag | Description |
| --- | --- |
| `-r`, `--read-only` | Generate a read-only token that allows queries but blocks writes |
| `-p`, `--permissions` | Define fine-grained permissions for each table and action |
| `-e`, `--expiration` | Set token expiration (`never`, `7d`, `30d`, etc.) |

```
turso db tokens create mydb --read-only

```

 Tokens may also be generated through the [Platform API](https://docs.turso.tech/api-reference/databases/create-token).

### Invalidating Tokens

 To rotate signing keys, invalidate all current tokens for a database or group:

```
# Invalidate all tokens for a database
turso db tokens invalidate <database-name>

# Invalidate all tokens for a group (and all its databases)
turso group tokens invalidate <group-name>

```
