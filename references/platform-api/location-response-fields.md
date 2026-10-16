# Location response fields

Open when constructing or interpreting the nested fields used by the linked Platform API operation. Array-item rows describe one element; a required flag applies within its immediate object. A blank required set in the source is not a guarantee that a response field will always be omitted or present.

## Response: `locations`

| Field | Type | Required in source | Meaning and constraints |
| --- | --- | --- | --- |
| `<location-code>` | string | Not declared | Location display name. |

## Operations

- Open when using `GET /v1/locations`: [official operation contract](https://docs.turso.tech/api-reference/locations/list).
