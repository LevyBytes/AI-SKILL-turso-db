# Show database access rules

[Context index](database-and-group-commands.md) · [Setup and preceding context](api-token-list-and-scopes.md) · [Previous phase](set-database-access-rules.md) · [Continue](database-creation-options.md)

## Show

Display a database's access allow rules.

```
turso db config allow-rules show <database-name>

```

 If no rules exist, the database permits every connection and the command outputs:

```
Access allow rules are empty: connections from any source are accepted

```

 If rules exist, the command outputs each list:

```
Allowed IPs:
  203.0.113.7
  10.0.0.0/8
Allowed AWS VPC endpoint IDs:
  vpce-0fe6c8807461bba49

```
