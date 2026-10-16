# Secrets vault schema and encrypted connection

[Context index](caching-secrets-and-vector-workflows.md) · [Continue](secrets-vault-use-redaction-and-auditing.md)

## Schema

AI coding agents often need to operate with real credentials, such as a GitHub API token, a billing-service key, or a deployment token. Pasting those secrets into chat is easy, but it leaves them in model context, transcripts, and provider logs. With an encrypted Turso database as a vault, the agent can use a secret without reading it: application code injects the value into a child process, runs the command, and redacts the captured output before returning anything. This guide explains the schema, encrypted connection setup, and queries for a local vault that allows use without disclosure.

### Schema

 The vault needs two tables: one table for stored secrets and another append-only table for audits.

```
CREATE TABLE IF NOT EXISTS secrets (
  name         TEXT PRIMARY KEY,
  value        TEXT NOT NULL,
  provider     TEXT,
  account      TEXT,
  environment  TEXT,
  access       TEXT,
  tags         TEXT NOT NULL DEFAULT '',
  description  TEXT,
  created_at   TEXT NOT NULL,
  last_used_at TEXT
);

CREATE TABLE IF NOT EXISTS audit_log (
  id         INTEGER PRIMARY KEY AUTOINCREMENT,
  ts         TEXT NOT NULL,
  secrets    TEXT NOT NULL,
  command    TEXT NOT NULL,
  cwd        TEXT NOT NULL,
  exit_code  INTEGER,
  outcome    TEXT NOT NULL
);

```

### How it fits together

- **`secrets`** keeps credentials under the `name` key. The `value` field contains the secret and is accessed only for child-process injection, not for agent display. Metadata fields (`provider`, `account`, `environment`, `access`, `tags`) let an agent choose a credential from attributes without exposing the underlying value.
- **`audit_log`** appends one record for each use, including injected secrets, command text, working directory, exit code, and whether the outcome was `ran` or `denied`.

### Connecting

 The vault lives in one encrypted database file. Supplying a key when connecting makes Turso encrypt pages at rest, while `multiprocess_wal` allows multiple processes, such as agent sessions and a CLI, to open the file safely at the same time:

```
import { connect } from "@tursodatabase/database";

const db = await connect("vault.db", {
  encryption: { cipher: "aes256gcm", hexkey },
  experimental: ["multiprocess_wal"],
});
await db.exec(SCHEMA); // The CREATE TABLE statements above

```

 `hexkey` must be a 64-character hex string. Its source is your choice: derive it from a passphrase with scrypt and avoid storing it, unwrap it from the OS keychain, or retrieve it from a cloud KMS. Turso only requires the key during `connect()`; storage behaves the same no matter where the key originated.

### Storing a secret

 Use `name` as the upsert key so changing a secret value preserves its metadata:

```
INSERT INTO secrets (name, value, provider, account, environment, access, tags, description, created_at)
VALUES (?, ?, ?, ?, ?, ?, ?, ?, ?)
ON CONFLICT(name) DO UPDATE SET
  value       = excluded.value,
  provider    = COALESCE(excluded.provider, secrets.provider),
  account     = COALESCE(excluded.account, secrets.account),
  environment = COALESCE(excluded.environment, secrets.environment),
  access      = COALESCE(excluded.access, secrets.access),
  description = COALESCE(excluded.description, secrets.description);

```
