# Create organization invite

[Context index](organization-membership-and-billing-api.md)

## Authorizations

```
curl -L -X POST https://api.turso.tech/v2/organizations/{organizationSlug}/invites \
  -H 'Authorization: Bearer TOKEN' \
  -H 'Content-Type: application/json' \
  -d '{"email": "user@example.com", "role": "member"}'

```

```
{
  "invited": {
    "email": "iku@turso.tech",
    "role": "member",
    "organization": "mycompany"
  }
}
```

POST /v2/organizations/{organizationSlug}/invites

Invitations require the scaler plan or a higher plan. Send an invite using either **email** or **username**, but not both. If you invite by username, Turso delivers the invite to the email registered for that user. Inviting users requires the `owner` or `admin` role. **Users can be invited only to a team, not to your personal account.** A new invite replaces any pending invite for the same email.

#### Authorizations

**`Authorization`**: string; header; required.

Supply Bearer auth using `Bearer <token>`; here, `<token>` is your auth token.

#### Path Parameters

**`organizationSlug`**: string; required.

Slug identifying either the organization or the user account.

#### Body

application/json

User details for the Turso invitation and the organization they will join. Include exactly one of email or username.

**`email`**: string.

Email address for the invitee. Provide exactly one of email or username.

**`username`**: string.

Username for an existing Turso user to invite. Provide exactly one of email or username.

**`role`** - enum&lt;string&gt;; default: member.

Role assigned to the user.

Available options: `admin`, `member`, `viewer`

#### Response

200 - application/json

Successful response

**`invited`**: object.

Open when you need the nested `invited` contract: [field details](organization-invite-created-fields.md).
