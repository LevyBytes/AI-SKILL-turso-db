# Organization usage response fields

Open when constructing or interpreting the nested fields used by the linked Platform API operation. Array-item rows describe one element; a required flag applies within its immediate object. A blank required set in the source is not a guarantee that a response field will always be omitted or present.

## Response: `organization`

| Field | Type | Required in source | Meaning and constraints |
| --- | --- | --- | --- |
| `uuid` | string | Not declared | The UUID of the organization. |
| `usage` | object | Not declared | The usage object for the organization. |
| `usage.rows_read` | integer | Not declared | Observed organization usage for this measure; not the subscription quota. |
| `usage.rows_written` | integer | Not declared | Observed organization usage for this measure; not the subscription quota. |
| `usage.databases` | integer | Not declared | Observed organization usage for this measure; not the subscription quota. |
| `usage.locations` | integer | Not declared | Observed organization usage for this measure; not the subscription quota. |
| `usage.storage_bytes` | integer | Not declared | Observed organization usage for this measure; not the subscription quota. |
| `usage.groups` | integer | Not declared | Observed organization usage for this measure; not the subscription quota. |
| `usage.bytes_synced` | integer | Not declared | Observed organization usage for this measure; not the subscription quota. |
| `databases` | object[] | Not declared | The databases for the organization and their usage. |
| `databases[].uuid` | not declared by source schema | Not declared | The source contains a literal where a property schema is expected; the field name is retained without inventing its type. |
| `databases[].instances` | object[] | Not declared | The usage objects for instances of the current database. |
| `databases[].instances[].uuid` | string | Not declared | The instance universal unique identifier (UUID). |
| `databases[].instances[].usage` | object | Not declared | The usage for the current database instance. |
| `databases[].instances[].usage.rows_read` | integer | Not declared | The total rows read in the time period. |
| `databases[].instances[].usage.rows_written` | integer | Not declared | The total rows written in the time period. |
| `databases[].instances[].usage.storage_bytes` | integer | Not declared | The total storage used. |
| `databases[].instances[].usage.bytes_synced` | integer | Not declared | The total bytes synced. |
| `databases[].total` | object | Not declared | The total usage for the database. |
| `databases[].total.rows_read` | integer | Not declared | The total rows read in the time period. |
| `databases[].total.rows_written` | integer | Not declared | The total rows written in the time period. |
| `databases[].total.storage_bytes` | integer | Not declared | The total storage used. |
| `databases[].total.bytes_synced` | integer | Not declared | The total bytes synced. |

## Operations

- Open when using `GET /v1/organizations/{organizationSlug}/usage`: [official operation contract](https://docs.turso.tech/api-reference/organizations/usage).
