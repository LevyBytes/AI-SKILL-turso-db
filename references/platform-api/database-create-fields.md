# Database create fields

Open when constructing or interpreting the nested fields used by the linked Platform API operation. Array-item rows describe one element; a required flag applies within its immediate object. A blank required set in the source is not a guarantee that a response field will always be omitted or present.

## Body: `seed`

| Field | Type | Required in source | Meaning and constraints |
| --- | --- | --- | --- |
| `type` | string | Not declared | The type of seed to be used to create a new database. Use `database` to copy from an existing database, or `database_upload` to [upload a SQLite database file](https://docs.turso.tech/api-reference/databases/upload). Values: `database`, `database_upload`. |
| `name` | string | Not declared | The name of the existing database when `database` is used as a seed type. |
| `timestamp` | string | Not declared | ISO 8601 recovery point within the plan retention window; check the PITR reference for current plan limits. |

## Body: `remote_encryption`

| Field | Type | Required in source | Meaning and constraints |
| --- | --- | --- | --- |
| `encryption_key` | string | Not declared | Base64-encoded encryption key. Key size depends on cipher: 32 bytes for aes256gcm, chacha20poly1305, aegis256 variants; 16 bytes for aes128gcm, aegis128l variants. |
| `encryption_cipher` | string | Not declared | The encryption cipher to use. Values: `aes256gcm`, `aes128gcm`, `chacha20poly1305`, `aegis128l`, `aegis128x2`, `aegis128x4`, `aegis256`, `aegis256x2`, `aegis256x4`. |

## Response: `database`

| Field | Type | Required in source | Meaning and constraints |
| --- | --- | --- | --- |
| `DbId` | not declared by source schema | Not declared | The source contains a literal where a property schema is expected; the field name is retained without inventing its type. |
| `Hostname` | not declared by source schema | Not declared | The source contains a literal where a property schema is expected; the field name is retained without inventing its type. |
| `Name` | not declared by source schema | Not declared | The source contains a literal where a property schema is expected; the field name is retained without inventing its type. |

## Operations

- Open when using `POST /v1/organizations/{organizationSlug}/databases`: [official operation contract](https://docs.turso.tech/api-reference/databases/create).
