# Organization subscription response fields

Open when constructing or interpreting the nested fields used by the linked Platform API operation. Array-item rows describe one element; a required flag applies within its immediate object. A blank required set in the source is not a guarantee that a response field will always be omitted or present.

## Response: `subscription`

| Field | Type | Required in source | Meaning and constraints |
| --- | --- | --- | --- |
| `name` | string | Not declared | The name of the plan for the current subscription. |
| `overages` | boolean | Not declared | Whether paid overages are enabled for the organization. |
| `plan` | string | Not declared | The name of the plan for the current subscription. |
| `timeline` | string | Not declared | Whether the plan is billed monthly or yearly. |

## Operations

- Open when using `GET /v1/organizations/{organizationSlug}/subscription`: [official operation contract](https://docs.turso.tech/api-reference/organizations/subscription).
