# Database usage response fields

Open when constructing or interpreting the nested fields used by the linked Platform API operation. Array-item rows describe one element; a required flag applies within its immediate object. A blank required set in the source is not a guarantee that a response field will always be omitted or present.

## Response: `database`

| Field | Type | Required in source | Meaning and constraints |
| --- | --- | --- | --- |
| `uuid` | not declared by source schema | Not declared | The source contains a literal where a property schema is expected; the field name is retained without inventing its type. |
| `instances` | object[] | Not declared | The usage objects for instances of the current database. |
| `instances[].uuid` | string | Not declared | The instance universal unique identifier (UUID). |
| `instances[].usage` | object | Not declared | The usage for the current database instance. |
| `instances[].usage.rows_read` | integer | Not declared | The total rows read in the time period. |
| `instances[].usage.rows_written` | integer | Not declared | The total rows written in the time period. |
| `instances[].usage.storage_bytes` | integer | Not declared | The total storage used. |
| `instances[].usage.bytes_synced` | integer | Not declared | The total bytes synced. |
| `total` | object | Not declared | The total usage for the database. |
| `total.rows_read` | integer | Not declared | The total rows read in the time period. |
| `total.rows_written` | integer | Not declared | The total rows written in the time period. |
| `total.storage_bytes` | integer | Not declared | The total storage used. |
| `total.bytes_synced` | integer | Not declared | The total bytes synced. |

## Operations

- Open when using `GET /v1/organizations/{organizationSlug}/databases/{databaseName}/usage`: [official operation contract](https://docs.turso.tech/api-reference/databases/usage).
