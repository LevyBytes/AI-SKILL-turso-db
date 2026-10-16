# Secrets vault use redaction and auditing

[Context index](caching-secrets-and-vector-workflows.md) · [Setup and preceding context](secrets-vault-schema-and-encrypted-connection.md)

### Listing without exposing values

 The agent-facing list query is written so it does not select the `value` column:

```
SELECT name, provider, account, environment, access, tags, description, created_at, last_used_at
FROM secrets ORDER BY name;

```

 The result includes names and metadata, giving the agent enough information to select a credential while the secret value remains inside the database.

### Using a secret

 To use a credential, retrieve its value, place it in the environment of a child process, execute the command, then redact that value from captured output before returning results:

```
SELECT value FROM secrets WHERE name = ?;

```

 Application code performs both environment injection and output redaction; the database does not do that work:

```
const { value } = await db.prepare(
  "SELECT value FROM secrets WHERE name = ?"
).get([name]);

const result = await runCommand(command, {
  env: { ...process.env, [name]: value },
});

// Replace every occurrence of the value before the agent sees the output
const safe = result.stdout.replaceAll(value, "***");

```

 Redacting captured output does not create a sandbox. Any command able to read a secret could intentionally disclose it by writing a file or sending network traffic. The vault offers a smaller guarantee that is still valuable: the *model* does not receive the value, and accidental output such as `echo $TOKEN` is masked. The secret exists only in the encrypted file and in the temporary process that consumed it.

### Auditing every use

 For every access, append an audit row and refresh the secret's `last_used_at` value:

```
INSERT INTO audit_log (ts, secrets, command, cwd, exit_code, outcome)
VALUES (?, ?, ?, ?, ?, ?);

UPDATE secrets SET last_used_at = ? WHERE name = ?;

```

 Questions such as who used the production key, at what time, and for which command become ordinary database queries:

```
SELECT ts, secrets, command, cwd, exit_code, outcome
FROM audit_log ORDER BY id DESC LIMIT ?;

```

### Key design points

- **Encrypted at rest, for free.** Turso provides whole-database encryption with AES-256-GCM or faster AEGIS options. The vault does not implement ciphers or page handling itself; it derives a key and passes it to `connect()`.
- **No coordinating daemon.** With `multiprocess_wal`, each agent session or CLI run can open the encrypted file directly, using the database itself as shared state instead of relying on a persistent server.
- **The value never appears in a list.** The listing path uses a `SELECT` that leaves out `value`, so agents see only names and attributes.
- **Fail closed.** If the requested secret name is unknown, execution stops before the command starts instead of running with no credential.
- **Auditing is just a table.** Each use creates an `INSERT`, and reports are `SELECT` queries that can feed exports or alerts.

### Example

 [keymaxxer](https://github.com/glommer/keymaxxer) is an MCP server that applies this approach as an encrypted secrets vault for AI coding agents.
