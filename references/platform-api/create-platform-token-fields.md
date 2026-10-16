# Create platform token fields

[Context index](api-quickstart-tokens-and-user.md) · [Setup and preceding context](create-platform-token-request.md)

#### Body

application/json

Optional token restriction. Leave the body out to create an unrestricted token; provide only `organization` for org scope, or provide `organization` + `group` + `scopes` when the token should be group-scoped.

**`organization`** — string.

Organization slug used to constrain this token. It is required if `group` is provided.

Example:

`"my-org"`

**`group`** — string.

Group name within `organization` that this token is limited to. This requires `organization` plus a non-empty `scopes` list.

Example:

`"default"`

**`scopes`** — enum&lt;string&gt;[].

Permissions assigned to a group-scoped token. Entries may be single scopes or the presets `read-only` (which maps to `read`) and `full-access` (which maps to all scopes). When `group` is supplied, this list is required and cannot be empty. `db:mint-token` can mint new SQL credentials, while `db:rotate-creds` invalidates all existing SQL tokens for the database; rotation is destructive, so these permissions are kept separate.

Supported options: `read`, `db:create`, `db:delete`, `db:configure`, `db:mint-token`, `db:rotate-creds`, `group:configure`, `group:mint-token`, `group:rotate-creds`, `read-only`, `full-access` Example:

```
[
  "db:create",
  "db:configure",
  "db:mint-token"
]
```

#### Response

200 - application/json

Successful response

**`name`** — unspecified.

**`id`** — unspecified.

**`token`** — string.

The token value itself, returned as a JWT. Use it in the `Bearer` header; for background, see [Authentication](https://docs.turso.tech/authentication). It will not be shown again.

Example:

`"..."`
