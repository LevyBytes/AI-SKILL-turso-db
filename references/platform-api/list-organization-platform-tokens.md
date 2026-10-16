# List organization platform tokens

[Context index](api-quickstart-tokens-and-user.md)

## Authorizations

```
curl -L 'https://api.turso.tech/v1/organizations/{organizationSlug}/api-tokens' \
  -H 'Authorization: Bearer TOKEN'

```

```
{
  "tokens": [
    {
      "name": "ci-bot",
      "id": "clGFZ4STEe6fljpFzIum8A",
      "organization": "my-org",
      "group": "default",
      "scopes": [
        "db:create",
        "db:configure",
        "db:mint-token"
      ],
      "owner": {
        "username": "alice",
        "email": "alice@example.com"
      },
      "created_at": "2026-05-21"
    }
  ]
}
```

GET /v1/organizations/{organizationSlug}/api-tokens

This endpoint lists tokens that are valid for one organization, including org-scoped and group-scoped tokens. Admins and owners can view every organization token and the user who minted it; members and viewers receive only their own tokens. To enumerate tokens for all organizations the caller belongs to, call [`GET /v1/auth/api-tokens`](https://docs.turso.tech/api-reference/tokens/list) instead.

#### Authorizations

**`Authorization`** — string; header; required.

Send a Bearer authentication header as `Bearer <token>`; `<token>` must be your auth token.

#### Path Parameters

**`organizationSlug`** — string; required.

Slug for the organization or user account.

#### Response

200 - application/json

Successful response

**`tokens`** — object[].

Open when you need the nested `tokens` contract: [field details](organization-token-response-fields.md).
