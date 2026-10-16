# Set database access rules

[Context index](database-and-group-commands.md) · [Setup and preceding context](api-token-list-and-scopes.md) · [Previous phase](clear-database-access-rules.md) · [Continue](show-database-access-rules.md)

## `turso db config allow-rules set`

Configure database access allow rules. A supplied flag overwrites its matching list, while omitted flags leave their lists as they are.

```
turso db config allow-rules set <database-name> [--ip <address-or-cidr>]... [--aws-vpc <vpce-id>]...

```

 Provide `--ip`, `--aws-vpc`, or both. To make access unrestricted, run [`turso db config allow-rules clear`](https://docs.turso.tech/cli/db/config/allow-rules/clear).

### Flags

| Flag | Description |
| --- | --- |
| `--ip` | Allowed IP address or CIDR block. Repeatable. Overwrites the existing IP list. |
| `--aws-vpc` | Allowed AWS VPC endpoint ID (`vpce-...`). Repeatable. Overwrites the existing VPC list. |

### Examples

### Allow a single IP

```
turso db config allow-rules set my-db --ip 203.0.113.7

```

### Allow a CIDR range

```
turso db config allow-rules set my-db --ip 10.0.0.0/8

```

### Allow multiple IPs

```
turso db config allow-rules set my-db --ip 203.0.113.7 --ip 10.0.0.0/8

```

### Restrict to an AWS VPC endpoint

```
turso db config allow-rules set my-db --aws-vpc vpce-0fe6c8807461bba49

```

### Combine IP and VPC rules

 If both lists exist, a connection has to meet both requirements.

```
turso db config allow-rules set my-db \
  --ip 10.0.0.0/8 \
  --aws-vpc vpce-0fe6c8807461bba49

```
