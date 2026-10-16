# Group migration creation and destruction

[Context index](installation-organization-and-account-commands.md) · [Setup and preceding context](api-token-list-and-scopes.md) · [Previous phase](local-libsql-server-and-database-guis.md) · [Continue](group-token-creation-and-invalidation.md)

## Abort

Abort the AWS migration process with this command:

```
turso group aws-migration abort <group-name>

```

## Info

Use this command to view details for the AWS migration:

```
turso group aws-migration info <group-name>

```

## Start

Start the AWS migration process by running this command:

```
turso group aws-migration start <group-name>

```

## `turso group create`

Create another database group. Every group is attached to one primary region.

```
turso group create <group-name> [flags]

```

 Only Scaler, Pro, and Enterprise plans can create multiple groups. Turso chooses the nearest region automatically as the primary region, unless you set a different one with the `--location` flag.

### Flags

| Flag | Description |
| --- | --- |
| `--canary` | Use database canary build. |
| `--location` | Use a three-digit code to create the group in the requested primary location. |
| `-w`, `--wait` | Do not exit until the group is ready. |

## `turso group destroy`

Destroy a group together with every database in it by using this command:

```
turso group destroy <group-name> [flags]

```

### Flags

| Flag | Description |
| --- | --- |
| `-y`, `--yes` | Confirms destruction of the group, including every location and database. |

## List

Run this command to retrieve all groups:

```
turso group list

```
