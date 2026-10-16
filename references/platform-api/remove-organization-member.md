# Remove organization member

[Context index](organization-membership-and-billing-api.md)

## Authorizations

```
curl -L -X DELETE https://api.turso.tech/v1/organizations/{organizationSlug}/members/{username} \
  -H 'Authorization: Bearer TOKEN'

```

```
{
  "member": "<unknown>"
}
```

DELETE /v1/organizations/{organizationSlug}/members/{username}

#### Authorizations

**`Authorization`** — string; header; required.

Include a Bearer auth header in the `Bearer <token>` shape, using your auth token as `<token>`.

#### Path Parameters

**`organizationSlug`** — string; required.

Slug for the target organization or user account.

**`username`** — string; required.

Username for either a Turso user or an organization member.

#### Response

Successful response

**`member`** — any.
