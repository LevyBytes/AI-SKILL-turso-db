# Plan price and quota fields

Open when constructing or interpreting the nested fields used by the linked Platform API operation. Array-item rows describe one element; a required flag applies within its immediate object. A blank required set in the source is not a guarantee that a response field will always be omitted or present.

## Response: `plans`

| Field | Type | Required in source | Meaning and constraints |
| --- | --- | --- | --- |
| `name` | string | Not declared | The name of the plan. |
| `price` | string | Not declared | The monthly price of the plan. |
| `prices` | object[] | Not declared | Nested field in the object shown above. |
| `prices[].value` | string | Not declared | Price of the available plan. |
| `prices[].timeline` | string | Not declared | Payment regularity. |
| `quotas` | object | Not declared | Nested field in the object shown above. |
| `quotas.rowsRead` | integer | Not declared | The number of rows read allowed for the specific plan. |
| `quotas.rowsWritten` | integer | Not declared | The number of rows written allowed for the specific plan. |
| `quotas.databases` | integer or null | Not declared | The number of databases allowed for the specific plan. |
| `quotas.locations` | integer | Not declared | The number of locations allowed for the specific plan. |
| `quotas.storage` | integer | Not declared | The amount of storage allowed for the specific plan, in bytes. |
| `quotas.groups` | integer | Not declared | The number of groups allowed for the specific plan. |
| `quotas.bytesSynced` | integer | Not declared | The number of bytes synced allowed for the specific plan, in bytes. |

## Operations

- Open when using `GET /v1/organizations/{organizationSlug}/plans`: [official operation contract](https://docs.turso.tech/api-reference/organizations/plans).
