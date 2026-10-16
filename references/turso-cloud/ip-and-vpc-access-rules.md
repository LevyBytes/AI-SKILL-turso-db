# IP and VPC access rules

[Context index](access-durability-encryption-and-migration.md) · [Continue](clear-access-rules-and-api-configuration.md)

This reference covers allow-rule inspection, durability guarantees, agent-oriented encryption, platform limits, and SQLite migration preparation.

## Show Current Rules

Allow rules restrict database access to approved connection origins. Sources can be IP addresses, CIDR ranges, AWS VPC endpoint IDs, or a combination of those controls. **AND semantics.** If both lists exist, the connection has to pass both checks: the client IP needs to appear in the allowed-IP list, and the route must use an allowed VPC endpoint.

### Show Current Rules

```
turso db config allow-rules show <database-name>

```

 With no configured rules, connections from any source are accepted:

```
Access allow rules are empty: connections from any source are accepted

```

 After rules are configured, the command displays both lists:

```
Allowed IPs:
  203.0.113.7
  10.0.0.0/8
Allowed AWS VPC endpoint IDs:
  vpce-0fe6c8807461bba49

```

### Restrict by IP Address or CIDR

 Pass `--ip` one or more times to define the allowed IP addresses and CIDR blocks. Each use **replaces** the previous list.

```
# Allow a single IP
turso db config allow-rules set my-db --ip 203.0.113.7

# Allow a CIDR range
turso db config allow-rules set my-db --ip 10.0.0.0/8

# Allow multiple entries at once
turso db config allow-rules set my-db --ip 203.0.113.7 --ip 10.0.0.0/8

```

 IPv4 and IPv6 addresses both work. For ranges, CIDR notation such as `10.0.0.0/8` is supported.

### Restrict by AWS VPC Endpoint

 Pass `--aws-vpc` one or more times to define the allowed [AWS VPC endpoint IDs](https://docs.turso.tech/cloud/private-endpoints). Each ID must begin with `vpce-`.

```
turso db config allow-rules set my-db --aws-vpc vpce-0fe6c8807461bba49

```

### Combine IP and VPC Rules

 Both lists may be configured by one command. A connection has to meet both conditions.

```
turso db config allow-rules set my-db \
  --ip 10.0.0.0/8 \
  --aws-vpc vpce-0fe6c8807461bba49

```

 If a later command includes only `--ip`, the VPC list stays as-is; the reverse is also true:

```
# Replace the IP list without touching the VPC list
turso db config allow-rules set my-db --ip 198.51.100.5

```
