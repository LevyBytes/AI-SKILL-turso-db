# Organization invoice response fields

Open when constructing or interpreting the nested fields used by the linked Platform API operation. Array-item rows describe one element; a required flag applies within its immediate object. A blank required set in the source is not a guarantee that a response field will always be omitted or present.

## Response: `invoices`

| Field | Type | Required in source | Meaning and constraints |
| --- | --- | --- | --- |
| `invoice_number` | string | Not declared | The unique ID for the invoice. |
| `amount_due` | string | Not declared | The formatted price in USD for the invoice. |
| `due_date` | string | Not declared | The due date for the invoice. |
| `paid_at` | string | Not declared | The date the invoice was paid. |
| `payment_failed_at` | string | Not declared | The date the invoice payment last failed. |
| `invoice_pdf` | string | Not declared | The link to the invoice PDF you can download. |

## Operations

- Open when using `GET /v1/organizations/{organizationSlug}/invoices`: [official operation contract](https://docs.turso.tech/api-reference/organizations/invoices).
