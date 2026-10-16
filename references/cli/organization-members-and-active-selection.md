# Organization members and active selection

[Context index](installation-organization-and-account-commands.md) · [Setup and preceding context](api-token-list-and-scopes.md) · [Previous phase](organization-billing-and-lifecycle.md) · [Continue](account-plans-overages-and-limits.md)

## `turso org members add`

Add an existing Turso user to the current active organization by running this command:

```
turso org members add <username> [flags]

```

### Flags

| Flag | Description |
| --- | --- |
| `-a`, `--admin` | Grant the user admin status. |

## `turso org members invite`

Invite a person who is not yet a Turso user to the current active organization with this command:

```
turso org members invite <email> [flags]

```

### Flags

| Flag | Description |
| --- | --- |
| `-a`, `--admin` | Grant the user admin status. |

## List

Run this command to list every member of the current active organization:

```
turso org members list

```

 This command works only with active organizations; use [`org switch`](https://docs.turso.tech/cli/org/switch) if you are authenticated under your personal account.

## Rm

Remove a member from the current organization with this command:

```
turso org members rm <username>

```

## Switch

Use this command to change the CLI's current active organization:

```
turso org switch <organization-slug>

```
