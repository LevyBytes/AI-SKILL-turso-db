# Retrieve organization member

[Context index](organization-membership-and-billing-api.md)

## Authorizations

```
curl -L https://api.turso.tech/v1/organizations/{organizationSlug}/members/{username} \
  -H 'Authorization: Bearer TOKEN'

```

```
{
  "member": {
    "username": "iku",
    "role": "owner",
    "email": "iku@turso.tech"
  }
}
```

GET /v1/organizations/{organizationSlug}/members/{username}

#### Authorizations

**`Authorization`** — string; header; required.

Send Bearer credentials as `Bearer <token>`; substitute your auth token for `<token>`.

#### Path Parameters

**`organizationSlug`** — string; required.

Organization or user account slug.

**`username`** — string; required.

Username identifying a Turso user or organization member.

#### Response

Successful response

**`member`** — object.

Open when you need the nested `member` contract: [field details](organization-member-response-fields.md).
