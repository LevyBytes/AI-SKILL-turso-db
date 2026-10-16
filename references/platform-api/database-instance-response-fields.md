# Database instance response fields

Open when constructing or interpreting the nested fields used by the linked Platform API operation. Array-item rows describe one element; a required flag applies within its immediate object. A blank required set in the source is not a guarantee that a response field will always be omitted or present.

## Response: `instances`

| Field | Type | Required in source | Meaning and constraints |
| --- | --- | --- | --- |
| `uuid` | string | Not declared | The instance universal unique identifier (UUID). |
| `name` | string | Not declared | The name of the instance (location code). |
| `type` | string | Not declared | The type of database instance this, will be `primary` or `replica`. Values: `primary`, `replica`. |
| `region` | string | Not declared | The location code for the region this instance is located. |
| `hostname` | string | Not declared | The DNS hostname used for client libSQL and HTTP connections (specific to this instance only). |

## Operations

- Open when using `GET /v1/organizations/{organizationSlug}/databases/{databaseName}/instances`: [official operation contract](https://docs.turso.tech/api-reference/databases/list-instances).
- Open when using `GET /v1/organizations/{organizationSlug}/databases/{databaseName}/instances/{instanceName}`: [official operation contract](https://docs.turso.tech/api-reference/databases/retrieve-instance).
