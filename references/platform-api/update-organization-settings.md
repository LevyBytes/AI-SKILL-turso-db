# Update organization settings

[Context index](organization-configuration-and-usage-api.md)

## Authorizations

```
curl -L -X PATCH https://api.turso.tech/v1/organizations/{organizationSlug} \
  -H 'Authorization: Bearer TOKEN' \
  -H 'Content-Type: application/json' \
  -d '{
      "overages": true
  }'

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

PATCH /v1/organizations/{organizationSlug}

#### Authorizations

**`Authorization`** — string; header; required.

Use Bearer authentication in the header as `Bearer <token>`; `<token>` should be the auth token for the request.

#### Path Parameters

**`organizationSlug`** — string; required.

Slug identifying the organization or user account.

#### Body

application/json

The updated organization details.

**`overages`** — boolean.

Controls whether organization overages are enabled or disabled.

**`require_mfa`** — boolean.

Make multi-factor authentication mandatory for every organization member. Before turning on this requirement, the requesting user must already have MFA enabled for their own account.

#### Response

200 - application/json

Successful response

**`organization`** — object.

The updated organization.

Open when you need the nested `organization` contract: [field details](organization-response-fields.md).
