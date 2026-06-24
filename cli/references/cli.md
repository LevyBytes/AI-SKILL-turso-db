# Auth, Authentication, Contact

> Cli reference. Original prose; identifiers preserved verbatim.

## Overview

Covers: List; Flags; Revoke; Token; Whoami; login; Bookmeeting; Feedback.

## List

Run this command to show every API token you created for organizations where you are a member:

```
turso auth api-tokens list

```

 The output is a table:

```
NAME            SCOPE                 PERMISSIONS                                CREATED AT
ci-bot          all                   —                                          2026-04-12
deploy-bot      my-org                —                                          2026-05-02
provisioner     my-org/default        read-only                                  2026-05-21
schema-bot      my-org/default        db:create, db:configure, db:mint-token     2026-05-23
admin-bot       my-org/default        full-access                                2026-05-24

```

- **Scope** shows how the token is bound:
  - `all` - no organization restriction; the token can act on every organization associated with the caller.
  - `<org>` — organization-scoped.
  - `<org>/<group>` — group-scoped.

- **Permissions** shows the grant recorded for group-scoped tokens:
  - `—` when no explicit scope is set, which applies to unrestricted and org-scoped tokens.
  - `read-only` means the only granted scope is `read`.
  - `full-access` means every scope is granted.
  - Otherwise, scopes are displayed as a comma-separated list.

 The output reflects the platform's stored representation. Creation presets like `--read-only` and `--full-access` are saved as the underlying individual scopes, so the list shows those expanded scopes.

## Flags

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

## Revoke

Run this command to revoke an API token in the current organization:

```
turso auth api-tokens revoke <api-token-name>

```

## Flags

Use this command to authenticate your Turso CLI account:

```
turso auth login

```

 When using Windows with WSL or running from remote CI/CD, pass the --headless flag.

### Flags

| Flag | Description |
| --- | --- |
| `--headless` | Use this when the CLI cannot open or control the system web browser. |

## Flags

Run this command to sign out of the Turso CLI:

```
turso auth logout

```

### Flags

| Flag | Description |
| --- | --- |
| `-a, --all` | Revoke all sessions for the current user. |

## Flags

Use this command to create a Turso account from the CLI:

```
turso auth signup

```

 For Windows with WSL, or for remote CI/CD runs, pass the --headless flag.

### Flags

| Flag | Description |
| --- | --- |
| `--headless` | Useful when the CLI cannot directly use the system web browser. |

## Token

Run this command to print the current API token used for Turso Platform API authentication:

```
turso auth token

```

## Whoami

Run this command to display the signed-in user's username:

```
turso auth whoami

```

## login

### `login`

 The Turso CLI authenticates to Turso with an access token. Log in with this command:

```
turso auth login

```

 The command opens a browser and asks you to sign in through GitHub. For Windows with WSL or remote CI/CD, use `--headless`. After login, account and database management commands are available. See the docs for all commands, or inspect a subcommand with `--help`:

```
turso

```

 The Turso CLI asks for authentication again after one week. Keep your token private.

### `signup`

 New Turso users can create an account from the Turso CLI:

```
turso auth signup

```

### `logout`

 Use `logout` to sign out of the Turso CLI:

```
turso auth logout

```

## Bookmeeting

Run this command to schedule a meeting with the Turso team about your project and requirements:

```
turso contact bookmeeting

```

 A webpage opens so you can choose the meeting date and time.

## Feedback

Use this command to send feedback to the Turso team:

```
turso contact feedback

```
