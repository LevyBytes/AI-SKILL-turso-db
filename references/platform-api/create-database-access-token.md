# Create database access token

[Context index](database-lifecycle-api-workflows.md)

## Authorizations

```
curl -L -X POST 'https://api.turso.tech/v1/organizations/{organizationSlug}/databases/{databaseName}/auth/tokens?expiration=2w&authorization=full-access' \
  -H 'Authorization: Bearer TOKEN'

```

```
{
  "jwt": "TOKEN"
}

```

POST /v1/organizations/{organizationSlug}/databases/{databaseName}/auth/tokens

#### Authorizations

**`Authorization`** — string; header; required.

Provide a Bearer authentication header as `Bearer <token>`, where `<token>` is your auth token.

#### Path Parameters

**`organizationSlug`** — string; required.

Slug identifying the organization or user account.

**`databaseName`** — string; required.

The name of the database.

#### Query Parameters

**`expiration`** — string; default: never.

Token lifetime, for example 2w1d30m.

**`authorization`** — enum&lt;string&gt;; default: full-access.

Token authorization level, either full-access or read-only.

Options available: `full-access`, `read-only`

#### Body

application/json

Extra token context, including any required claims.

**`permissions`** — object.

The permissions for the token.

Open when you need the nested `permissions` contract: [field details](token-attach-permission-fields.md).

#### Response

Successful response

**`jwt`** — string.

The generated authorization token (JWT).
