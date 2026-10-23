# Revoke organization invite

[Context index](organization-membership-and-billing-api.md)

## Authorizations

```
curl -L -X DELETE https://api.turso.tech/v2/organizations/{organizationSlug}/invites/{email} \
  -H 'Authorization: Bearer TOKEN'

```

```
{
  "error": "invite not found"
}
```

DELETE /v2/organizations/{organizationSlug}/invites/{email}

Invites require the scaler plan or a higher plan.

#### Authorizations

**`Authorization`**: string; header; required.

Authenticate with a Bearer header formatted as `Bearer <token>`; replace `<token>` with your auth token.

#### Path Parameters

**`organizationSlug`**: string; required.

Slug for the organization, or for the user account.

**`email`**: string; required.

Email address used for the invited user.

Example:

`"iku@turso.tech"`

#### Response

Successful response (No Content)
