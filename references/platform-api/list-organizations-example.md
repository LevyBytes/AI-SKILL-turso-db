# List organizations example

[Context index](organization-membership-and-billing-api.md) · [Continue](organization-list-response-fields.md)

## Authorizations

```
curl -L https://api.turso.tech/v1/organizations \
  -H 'Authorization: Bearer TOKEN'

```

```
[
  {
    "name": "personal",
    "slug": "iku",
    "type": "personal",
    "overages": false,
    "require_mfa": false,
    "blocked_reads": false,
    "blocked_writes": false,
    "plan_id": "developer",
    "plan_timeline": "monthly",
    "platform": "vercel"
  }
]
```

GET /v1/organizations

#### Authorizations

**`Authorization`** — string; header; required.

Provide Bearer authentication as `Bearer <token>`, where `<token>` contains your auth token.
