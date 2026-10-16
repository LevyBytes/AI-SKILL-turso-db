# Add organization member

[Context index](organization-membership-and-billing-api.md)

## Authorizations

Add Member

```
curl --request POST \
  --url https://api.turso.tech/v1/organizations/{organizationSlug}/members \
  --header 'Authorization: Bearer <token>' \
  --header 'Content-Type: application/json' \
  --data '
{
  "username": "<string>",
  "role": "member"
}
'
```

```
{
  "member": "<unknown>",
  "role": "<unknown>"
}
```

POST /v1/organizations/{organizationSlug}/members

Add Member

To add someone who has not registered with Turso, use [create an invite](https://docs.turso.tech/api-reference/organizations/invites/create) instead. Adding members requires the `owner` or `admin` role. **Users can be added only to a team, not to your personal account.**

#### Authorizations

**`Authorization`** — string; header; required.

Set the Bearer authorization header to `Bearer <token>` and use your auth token for `<token>`.

#### Path Parameters

**`organizationSlug`** — string; required.

Slug identifying the organization, or a user account slug.

#### Body

application/json

Member record to add.

**`username`** — string.

Username for an existing Turso user.

**`role`** — enum&lt;string&gt;; default: member.

Role assigned to the user.

Available options: `admin`, `member`, `viewer`

#### Response

Successful response

**`member`** — unspecified.

**`role`** — unspecified.
