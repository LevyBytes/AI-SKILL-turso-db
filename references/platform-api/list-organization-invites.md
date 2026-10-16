# List organization invites

[Context index](organization-membership-and-billing-api.md)

## Authorizations

```
curl -L https://api.turso.tech/v2/organizations/{organizationSlug}/invites \
  -H 'Authorization: Bearer TOKEN'

```

```
{
  "invites": [
    {
      "id": 1,
      "email": "iku@turso.tech",
      "role": "member",
      "created_at": "2023-01-01T00:00:00Z"
    }
  ]
}
```

GET /v2/organizations/{organizationSlug}/invites

#### Authorizations

**`Authorization`** — string; header; required.

Use Bearer authentication in the `Bearer <token>` header form, with `<token>` set to your auth token.

#### Path Parameters

**`organizationSlug`** — string; required.

Organization slug, or the matching user account slug.

#### Response

200 - application/json

Successful response

**`invites`** — object[].

Open when you need the nested `invites` contract: [field details](organization-invite-list-fields.md).
