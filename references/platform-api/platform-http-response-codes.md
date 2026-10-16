# Platform HTTP response codes

[Context index](api-quickstart-tokens-and-user.md)

## Response Codes

The Turso API returns an HTTP status code. If the request fails, the response body includes an error message.

| Code | Description |
| --- | --- |
| `200` | `OK` — Successful request |
| `401` | `Unauthorized` — Invalid or expired auth token |
| `402` | `Payment required` - Confirm that an active subscription is in place |
| `403` | `Forbidden` - Your credentials are not permitted to access this resource |
| `409` | `Conflict` — Resource already exists |
