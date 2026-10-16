# Platform token scopes and lifecycle

[Context index](audit-and-token-api-workflows.md)

## Group-scoped tokens

Turso API requests use API tokens for authentication. Create or revoke API tokens with the [Turso CLI](https://docs.turso.tech/cli) or the [Authentication API](https://docs.turso.tech/api-reference/tokens/create). Tokens can have three restriction levels:

| Restriction | What it can do | How to mint |
| --- | --- | --- |
| Organization-scoped | Restricted to a single organization. | `turso auth api-tokens mint my-token --org my-org` |
| Group-scoped | Restricted to one organization group with a selected permission set. | `turso auth api-tokens mint my-token --org my-org --group default --read-only` |
| Unrestricted *(deprecated)* | Allowed across every organization linked to the caller. | `turso auth api-tokens mint my-token` |

**Unrestricted (cross-org) tokens are on the removal path and should no longer be used for new credentials.** Create new tokens as organization-scoped tokens with `--org`. Existing unrestricted tokens continue to work for now, but should be rotated into scoped tokens soon.

```
# Token scoped to a single organization
turso auth api-tokens mint my-token --org my-org

# Group-scoped token, read-only (tighter scope, for automations that only need one group)
turso auth api-tokens mint my-token --org my-org --group default --read-only

# Token with access to all organizations — deprecated
turso auth api-tokens mint my-token

```

- Use `--org` to attach tokens to one organization, especially for production and CI/CD usage.
- Keep API tokens in environment variables when using them.
- Never publish your API token in repositories, CI/CD Actions, or other public places.
 Turso uses Bearer authentication, so protected calls must include your API token in the `Authorization` header:

```
Authorization: Bearer TOKEN

```

### Group-scoped tokens

 A group-scoped token is a Platform API credential limited to one group inside an organization and to the operations you grant. It is useful for automation that provisions or manages databases in that group without access to the rest of the organization. The token creator must be an organization admin or owner. These are control-plane credentials for the Turso Platform API, not the same as [SQL-engine tokens](https://docs.turso.tech/sdk/authorization/tokens) used by applications for database queries. The two systems use different key material and terms.

### Scope vocabulary

 The presets `read-only` and `full-access` are expanded on the server into their individual scopes, so created tokens display individual scope values afterward.

| Scope | Allows |
| --- | --- |
| `read` | All GET-style endpoints, including list and retrieve operations for databases, groups, configuration, usage, instances, and locations. |
| `db:create` | Create group databases with `POST /databases`, restore them, or seed them from a dump. |
| `db:delete` | Delete a database from the group. |
| `db:configure` | Change database configuration, transfer a database, wake it, or manage instances. |
| `db:mint-token` | Create a SQL-engine token or TLS client certificate for a group database. |
| `db:rotate-creds` | Rotate the database signing key, invalidating every previously issued SQL token for that database. |
| `group:configure` | Configure, rename, update, or unarchive the group, and manage group locations. |
| `group:mint-token` | Create a SQL-engine token at the group level. |
| `group:rotate-creds` | Rotate the group signing key, invalidating SQL tokens issued for all databases in that group. |

 `db:mint-token` and `db:rotate-creds` are deliberately separate scopes. Minting a SQL credential adds access; rotating credentials revokes active credentials and can disrupt running applications. Grant rotation only to automation that truly needs it.

### Presets

| Preset | Expands to |
| --- | --- |
| `read-only` | `read` |
| `full-access` | Every individual scope listed above. |

 Group-scoped tokens cannot create groups, delete groups, transfer groups, or run AWS migration at any scope; those actions need organization-level credentials.

### Lifecycle

- Group-scoped tokens bind to the group UUID, not the group name. If a group is deleted and later recreated with the same name, old tokens do not apply; deletion revokes them with the original group.
- Deleting a group revokes all tokens scoped to that group.
- Moving a group to another organization also revokes all tokens scoped to that group.

### Managing organization tokens

 Admins can manage organization-scoped tokens from two endpoints that also back the dashboard's organization-level token table:
- [`GET /v1/organizations/{organizationSlug}/api-tokens`](https://docs.turso.tech/api-reference/tokens/list-organization) lists org-scoped and group-scoped tokens, including the user who created each one.
- [`DELETE /v1/organizations/{organizationSlug}/api-tokens/{tokenId}`](https://docs.turso.tech/api-reference/tokens/revoke-organization) revokes a token by ID.
 Members and viewers can also call these endpoints, but they can only see and revoke tokens they created.

### Base URL

 Use this URL for Turso API requests:

```
https://api.turso.tech

```
