# List organization members

[Context index](organization-membership-and-billing-api.md)

## Authorizations

```
curl -L https://api.turso.tech/v1/organizations/{organizationSlug}/members \
  -H 'Authorization: Bearer TOKEN'

```

```
{
  "members": [
    {
      "username": "iku",
      "role": "owner",
      "email": "iku@turso.tech"
    }
  ]
}
```

GET /v1/organizations/{organizationSlug}/members

#### Authorizations

**`Authorization`**: string; header; required.

Authorize the request with `Bearer <token>` as the Bearer header value; `<token>` is your auth token.

#### Path Parameters

**`organizationSlug`**: string; required.

Organization slug or user account slug.

#### Response

200 - application/json

Successful response

**`members`**: object[].

Open when you need the nested `members` contract: [field details](organization-member-response-fields.md).
