# Retrieve organization settings

[Context index](organization-membership-and-billing-api.md)

## Authorizations

```
curl -L https://api.turso.tech/v1/organizations/{organizationSlug} \
  -H 'Authorization: Bearer TOKEN'

```

```
{
  "organization": {
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
}
```

GET /v1/organizations/{organizationSlug}

#### Authorizations

**`Authorization`**: string; header; required.

For authorization, send `Bearer <token>` and set `<token>` to your auth token.

#### Path Parameters

**`organizationSlug`**: string; required.

Slug of the organization or user account being updated.

#### Response

Successful response

**`organization`**: object.

Open when you need the nested `organization` contract: [field details](organization-response-fields.md).
