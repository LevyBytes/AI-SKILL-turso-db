# Database response fields

Open when constructing or interpreting the nested fields used by the linked Platform API operation. Array-item rows describe one element; a required flag applies within its immediate object. A blank required set in the source is not a guarantee that a response field will always be omitted or present.

## Response: `databases`

| Field | Type | Required in source | Meaning and constraints |
| --- | --- | --- | --- |
| `Name` | string | Not declared | The database name, **unique** across your organization. |
| `DbId` | string | Not declared | The database universal unique identifier (UUID). |
| `Hostname` | string | Not declared | The DNS hostname used for client libSQL and HTTP connections. |
| `block_reads` | boolean | Not declared | The current status for blocked reads. |
| `block_writes` | boolean | Not declared | The current status for blocked writes. |
| `regions` | string[] | Not declared | A list of regions for the group the database belongs to. Deprecated in this API contract. |
| `primaryRegion` | string | Not declared | The primary region location code the group the database belongs to. |
| `group` | string | Not declared | The name of the group the database belongs to. |
| `delete_protection` | boolean | Not declared | The current status for delete protection. If enabled, the database cannot be deleted. |
| `parent` | object or null | Not declared | Nested field in the object shown above. |
| `parent.id` | string | Not declared | The parent database identifier. |
| `parent.name` | string | Not declared | The name of the parent database. |
| `parent.branched_at` | string | Not declared | The timestamp when the database was branched from the parent. Format: `date-time`. |

## Operations

- Open when using `GET /v1/organizations/{organizationSlug}/databases`: [official operation contract](https://docs.turso.tech/api-reference/databases/list).
- Open when using `GET /v1/organizations/{organizationSlug}/databases/{databaseName}`: [official operation contract](https://docs.turso.tech/api-reference/databases/retrieve).
