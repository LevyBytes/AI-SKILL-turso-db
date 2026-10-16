# List organization plans

[Context index](organization-membership-and-billing-api.md)

## Authorizations

```
curl -L https://api.turso.tech/v1/organizations/{organizationSlug}/plans \
  -H 'Authorization: Bearer TOKEN'

```

```
{
  "plans": [
    {
      "name": "starter",
      "price": "0",
      "prices": [
        {
          "value": "29",
          "timeline": "monthly"
        }
      ],
      "quotas": {
        "rowsRead": 1000000000,
        "rowsWritten": 25000000,
        "databases": 500,
        "locations": 3,
        "storage": 9000000000,
        "groups": 1,
        "bytesSynced": 3000000000
      }
    }
  ]
}
```

GET /v1/organizations/{organizationSlug}/plans

#### Authorizations

**`Authorization`** — string; header; required.

Authenticate by sending `Bearer <token>` as the Bearer value, with `<token>` containing your auth token.

#### Path Parameters

**`organizationSlug`** — string; required.

Slug for the organization or user account being queried.

#### Response

200 - application/json

Successful response

**`plans`** — object[].

List of available plans.

Open when you need the nested `plans` contract: [field details](plan-price-and-quota-fields.md).
