# List organization invoices

[Context index](organization-membership-and-billing-api.md)

## Authorizations

```
curl -L https://api.turso.tech/v1/organizations/{organizationSlug}/invoices \
  -H 'Authorization: Bearer TOKEN'

```

```
{
  "invoices": [
    {
      "invoice_number": "LFONTK-00001",
      "amount_due": "10.29",
      "due_date": "2024-01-01T05:00:00+00:00",
      "paid_at": "2024-01-01T05:00:00+00:00",
      "payment_failed_at": "2024-01-01T05:00:00+00:00",
      "invoice_pdf": "https://assets.withorb.com/invoice/..."
    }
  ]
}
```

GET /v1/organizations/{organizationSlug}/invoices

#### Authorizations

**`Authorization`**: string; header; required.

Pass a Bearer authentication header as `Bearer <token>`; `<token>` must be your auth token.

#### Path Parameters

**`organizationSlug`**: string; required.

Slug value for the organization or user account.

#### Query Parameters

**`type`**: enum&lt;string&gt;.

Invoice type to retrieve.

Available options: `all`, `upcoming`, `issued`

#### Response

200 - application/json

Successful response

**`invoices`**: object[].

Invoices returned for the organization.

Open when you need the nested `invoices` contract: [field details](organization-invoice-response-fields.md).
