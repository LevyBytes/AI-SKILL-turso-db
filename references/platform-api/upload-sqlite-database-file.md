# Upload SQLite database file

[Context index](database-configuration-upload-and-usage.md)

## Headers

```
curl -X POST 'https://{database_hostname}/v1/upload' \
  -H 'Authorization: Bearer DATABASE_TOKEN' \
  -H 'Content-Length: FILE_SIZE' \
  --data-binary '@/path/to/database.db'

```

POST /v1/upload

Upload a SQLite database file into a database created with `seed.type = "database_upload"`. Call this endpoint on the **database hostname**, for example `my-db-my-org.turso.io`, instead of `api.turso.tech`. Before uploading, [create a database](https://docs.turso.tech/api-reference/databases/create) using `seed.type = "database_upload"`. Authentication for this endpoint uses a **database token**, not a platform API token. After the database exists, you can [create a database token](https://docs.turso.tech/api-reference/databases/create-token).

### Headers

| Header | Required | Description |
| --- | --- | --- |
| `Authorization` | Yes | Bearer token for the database |
| `Content-Length` | Yes | Database file size, in bytes |
| `x-turso-encryption-key` | No | Base64-encoded encryption key, using 32 bytes for 256-bit ciphers or 16 bytes for 128-bit ciphers |
| `x-turso-encryption-cipher` | No | Encryption cipher. Supported values are listed in the table below. |

### Request Body

 Raw binary SQLite database file.

### Database File Requirements

 The SQLite database file is required to satisfy the following conditions:
- **Journal mode**: WAL (`PRAGMA journal_mode = WAL`)
- **Page size**: 4096 bytes (`PRAGMA page_size = 4096`)
- **Auto vacuum**: Disabled (`PRAGMA auto_vacuum = 0`)
- **Encoding**: UTF-8 (`PRAGMA encoding = 'UTF-8'`)
 Encrypted databases must also configure reserved bytes for the selected cipher:

| Cipher | Reserved Bytes |
| --- | --- |
| `aes256gcm` | 28 |
| `aes128gcm` | 28 |
| `chacha20poly1305` | 28 |
| `aegis128l` | 32 |
| `aegis128x2` | 32 |
| `aegis128x4` | 32 |
| `aegis256` | 48 |
| `aegis256x2` | 48 |
| `aegis256x4` | 48 |

### Responses

### Success

 On success, returns no body and status `200 OK`.

### Errors

| Status | Description |
| --- | --- |
| `400` | Database file is invalid because of format, settings, or corruption |
| `401` | Invalid or missing authorization token |
