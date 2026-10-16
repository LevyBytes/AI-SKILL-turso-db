# Audit log requests and pagination

[Context index](audit-and-token-api-workflows.md)

## Authorizations

```
curl -L https://api.turso.tech/v1/organizations/{organizationSlug}/audit-logs \
  -H 'Authorization: Bearer TOKEN'

```

```
{
  "audit_logs": [
    {
      "code": "db-create",
      "message": "",
      "origin": "cli",
      "author": "iku",
      "created_at": "2023-12-20T09:46:08Z",
      "data": {}
    }
  ],
  "pagination": {
    "page": 1,
    "page_size": 10,
    "total_pages": 1,
    "total_rows": 1
  }
}
```

GET /v1/organizations/{organizationSlug}/audit-logs

Audit Logs require the scaler plan or a higher plan.

#### Authorizations

**`Authorization`** — string; header; required.

Send authentication as a Bearer header: `Bearer <token>`, replacing `<token>` with your auth token.

#### Path Parameters

**`organizationSlug`** — string; required.

Slug for the organization or user account.

#### Query Parameters

**`page_size`** — integer.

Maximum items returned per page. Defaults to 100.

**`page`** — integer.

Page number to retrieve. Defaults to 1.

#### Response

200 - application/json

Successful response

**`audit_logs`** — object[].

Open when you need the nested `audit_logs` contract: [field details](audit-log-response-fields.md).

**`pagination`** — object.

Open when you need the nested `pagination` contract: [field details](audit-log-response-fields.md).
