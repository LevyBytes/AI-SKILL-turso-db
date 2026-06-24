# Closest Region, List

> Platform Api reference. Original prose; identifiers preserved verbatim.

## Overview

Covers: Response; Authorizations.

## Response

```
curl https://region.turso.io

```

```
{
  "server": "lhr",
  "client": "lhr"
}

```

GET/

```
curl https://region.turso.io

```

```
{
  "server": "lhr",
  "client": "lhr"
}

```

#### Response

 serverstringThe response server location code. clientstringThe request client location code.

```
curl https://region.turso.io

```

```
{
  "server": "lhr",
  "client": "lhr"
}

```

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

GET/v1/locations

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

```
curl -L https://api.turso.tech/v1/locations \
  -H 'Authorization: Bearer TOKEN'

```

#### Authorizations

Authorizationstringheaderrequired

Bearer authentication header of the form `Bearer <token>`, where `<token>` is your auth token.

#### Response

200 - application/json

Successful response

locationsobject

A mapping of location codes to location names.

Show child attributes
