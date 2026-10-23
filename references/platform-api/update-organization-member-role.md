# Update organization member role

[Context index](organization-membership-and-billing-api.md)

## Authorizations

```
curl -L -X PATCH https://api.turso.tech/v1/organizations/{organizationSlug}/members/{username} \
  -H 'Authorization: Bearer TOKEN' \
  -H 'Content-Type: application/json' \
  -d '{
    "role": "member"
  }'

```

```
{
  "member": {
    "username": "<string>",
    "email": "<string>"
  }
}
```

PATCH /v1/organizations/{organizationSlug}/members/{username}

#### Authorizations

**`Authorization`**: string; header; required.

Use the Bearer header value `Bearer <token>` and place your auth token in `<token>`.

#### Path Parameters

**`organizationSlug`**: string; required.

Slug of the relevant organization or user account.

**`username`**: string; required.

Username of the Turso user or organization member.

#### Body

application/json

**`role`**: enum&lt;string&gt;; required.

New role to assign to the member.

Available options: `admin`, `member`, `viewer`

#### Response

Successful response

**`member`**: object.

Open when you need the nested `member` contract: [field details](organization-member-role-response-fields.md).
