# List available locations

[Context index](region-and-location-api-workflows.md)

## Authorizations

```
curl -L https://api.turso.tech/v1/locations \
  -H 'Authorization: Bearer TOKEN'

```

```
{
  "locations": {
    "aws-ap-northeast-1": "AWS AP NorthEast (Tokyo)",
    "aws-ap-south-1": "AWS AP South (Mumbai)",
    "aws-eu-west-1": "AWS EU West (Ireland)",
    "aws-us-east-1": "AWS US East (Virginia)",
    "aws-us-east-2": "AWS US East (Ohio)",
    "aws-us-west-2": "AWS US West (Oregon)"
  }
}
```

GET /v1/locations

#### Authorizations

**`Authorization`** — string; header; required.

Bearer authentication header of the form `Bearer <token>`, where `<token>` is your auth token.

#### Response

200 - application/json

Successful response

**`locations`** — object.

A mapping of location codes to location names.

Open when you need the nested `locations` contract: [field details](location-response-fields.md).
