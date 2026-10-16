# Group transfer unarchive and updates

[Context index](installation-organization-and-account-commands.md) · [Setup and preceding context](api-token-list-and-scopes.md) · [Previous phase](group-token-creation-and-invalidation.md) · [Continue](headless-authentication-and-command-help.md)

## `turso group transfer`

Transfer a group, including its databases, to an organization where you are an admin or owner with this command:

```
turso group transfer <group-name> <organization-name> [flags]

```

 Current database URL and tokens remain valid, but update your application to the new URL and token as soon as you can.

### Flags

| Flag | Description |
| --- | --- |
| `-y`, `--yes` | Confirms moving the group to a new organization. |

## Unarchive

Unarchive inactive databases by running this command:

```
turso group unarchive <group-name>

```

 For users on a free plan, databases are archived after 10 days without activity.

## `turso group update`

Update the group and all included databases with this command:

```
turso group update <group-name> [flags]

```

### Flags

| Flag | Description |
| --- | --- |
| `--extensions string` | Enable extensions by passing `all` or `none`. |
| `--version string` | Choose the group version to update to. Supported values include `latest` and `canary`. |
| `-y`, `--yes` | Skip confirmation prompt and confirm. |

### Examples

### Update a group to enable all extensions

 Enable `all` extensions by updating a group and all of its databases:

```
turso group update <group-name> --extensions all

```
