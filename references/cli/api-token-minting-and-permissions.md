# API token minting and permissions

[Context index](authentication-and-contact-commands.md) · [Setup and preceding context](api-token-list-and-scopes.md) · [Continue](revoke-tokens-and-manage-login.md)

## `turso auth api-tokens mint`

Use this command to create an API token limited to one organization:

```
turso auth api-tokens mint <api-token-name> --org <organization-slug>

```

 **Unrestricted (cross-org) tokens - created by omitting `--org` - are deprecated and planned for removal in a future release.** Always include `--org` for new tokens. Existing unrestricted tokens still work for now, but rotate them to scoped tokens as soon as practical.

### Flags

| Flag | Description |
| --- | --- |
| `--org` | Restrict the token to one organization. |
| `--group` | Restrict the token to one group inside `--org`; this requires `--org` and at least one scope. |
| `--scope` | Grant a permission on a group-scoped token. This flag can be repeated. |
| `--read-only` | Shorthand for `--scope read`. |
| `--full-access` | Shorthand for granting every scope. |

### Organization-scoped tokens

 Use `--org` to limit a token to one organization:

```
turso auth api-tokens mint <api-token-name> --org <organization-slug>

```

 An org-scoped token can access only resources in that organization, including groups, databases, and members. Requests for another organization are denied.

### Group-scoped tokens

 A group-scoped token is bound to one organization group and only the operations you grant. Use it for automation that should provision or manage databases in that group while keeping the rest of the organization out of reach. Creating a group-scoped token requires organization admin or owner privileges.

```
# Read-only access to one group
turso auth api-tokens mint deploy-bot --org my-org --group default --read-only

# Full access inside one group, but nothing outside it
turso auth api-tokens mint deploy-bot --org my-org --group default --full-access

# Fine-grained: provision and configure databases, mint SQL tokens
turso auth api-tokens mint deploy-bot --org my-org --group default \
  --scope db:create --scope db:configure --scope db:mint-token

```

### Available scopes

 `--scope` is repeatable and accepts these values:

| Scope | Allows |
| --- | --- |
| `read` | GET-style access for listing or retrieving databases and groups, plus configuration, usage, instances, and locations. |
| `db:create` | Create a database in the group, restore one, or seed it from a dump. |
| `db:delete` | Delete a database belonging to the group. |
| `db:configure` | Patch settings, transfer a database, wake it, or add and remove instances. |
| `db:mint-token` | Mint a SQL-engine token or a TLS client certificate for a group database. |
| `db:rotate-creds` | Rotate the signing key for a database, invalidating SQL tokens issued earlier for that database. |
| `group:configure` | Configure, rename, update, or unarchive the group, and add or remove locations. |
| `group:mint-token` | Mint a SQL-engine token at group level. |
| `group:rotate-creds` | Rotate the group's signing key, invalidating every SQL token issued for databases in the group. |

 The scopes `db:mint-token` and `db:rotate-creds` are deliberately separate. Minting a SQL credential adds a valid credential; rotating credentials revokes those currently in service and can interrupt live applications. Grant rotation only to automation that truly needs it. Use exactly one of `--scope`, `--read-only`, or `--full-access`; they are mutually exclusive. Unknown scope labels fail validation before any request is sent. Group create, group delete, group transfer, and AWS migration remain unavailable to group-scoped tokens regardless of scope.

### Lifecycle

 Group-scoped tokens are tied to the group's UUID rather than its name. If that group is deleted or moved to another organization, every token scoped to it is revoked. Reusing the same group name later does not revive earlier tokens. The token value is displayed only once at creation, so store it securely.
