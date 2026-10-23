# Retrieve organization subscription

[Context index](organization-membership-and-billing-api.md)

## Authorizations

```
curl -L https://api.turso.tech/v1/organizations/{organizationSlug}/subscription \
  -H 'Authorization: Bearer TOKEN'

```

```
{
  "subscription": {
    "name": "scaler",
    "overages": true,
    "plan": "scaler",
    "timeline": "monthly"
  }
}
```

GET /v1/organizations/{organizationSlug}/subscription

#### Authorizations

**`Authorization`**: string; header; required.

Use `Bearer <token>` for Bearer authentication; `<token>` represents your auth token.

#### Path Parameters

**`organizationSlug`**: string; required.

Slug that identifies the organization or user account.

#### Response

200 - application/json

Successful response

**`subscription`**: object.

Open when you need the nested `subscription` contract: [field details](organization-subscription-response-fields.md).
