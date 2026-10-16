# Clear database access rules

[Context index](database-and-group-commands.md) · [Setup and preceding context](api-token-list-and-scopes.md) · [Previous phase](contact-meetings-and-feedback.md) · [Continue](set-database-access-rules.md)

Topics covered: Flags; Show; Using a dump locally.

## `turso db config allow-rules clear`

Remove a database's access allow rules so connections may originate from anywhere.

```
turso db config allow-rules clear <database-name> [flags]

```

 With no flags, the command removes both the IP list and the AWS VPC endpoint list. Pass `--ips` or `--aws-vpcs` when only one list should be cleared.

### Flags

| Flag | Description |
| --- | --- |
| `--ips` | Remove entries only from the allowed IP list. |
| `--aws-vpcs` | Remove entries only from the allowed AWS VPC endpoint ID list. |

### Examples

### Clear all rules

```
turso db config allow-rules clear my-db

```

### Clear only the IP list

```
turso db config allow-rules clear my-db --ips

```

### Clear only the VPC list

```
turso db config allow-rules clear my-db --aws-vpcs

```
