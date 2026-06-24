# Allow Rules, Durability, Encryption, Limitations ...

> Turso Cloud reference. Original prose; identifiers preserved verbatim.

## Overview

This reference covers allow-rule inspection, durability guarantees, agent-oriented encryption, platform limits, and SQLite migration preparation.

## Show Current Rules

Allow rules restrict database access to approved connection origins. Sources can be IP addresses, CIDR ranges, AWS VPC endpoint IDs, or a combination of those controls. **AND semantics.** If both lists exist, the connection has to pass both checks: the client IP needs to appear in the allowed-IP list, and the route must use an allowed VPC endpoint.

### Show Current Rules

```
turso db config allow-rules show <database-name>

```

 With no configured rules, connections from any source are accepted:

```
Access allow rules are empty: connections from any source are accepted

```

 After rules are configured, the command displays both lists:

```
Allowed IPs:
  203.0.113.7
  10.0.0.0/8
Allowed AWS VPC endpoint IDs:
  vpce-0fe6c8807461bba49

```

### Restrict by IP Address or CIDR

 Pass `--ip` one or more times to define the allowed IP addresses and CIDR blocks. Each use **replaces** the previous list.

```
# Allow a single IP
turso db config allow-rules set my-db --ip 203.0.113.7

# Allow a CIDR range
turso db config allow-rules set my-db --ip 10.0.0.0/8

# Allow multiple entries at once
turso db config allow-rules set my-db --ip 203.0.113.7 --ip 10.0.0.0/8

```

 IPv4 and IPv6 addresses both work. For ranges, CIDR notation such as `10.0.0.0/8` is supported.

### Restrict by AWS VPC Endpoint

 Pass `--aws-vpc` one or more times to define the allowed [AWS VPC endpoint IDs](https://docs.turso.tech/cloud/private-endpoints). Each ID must begin with `vpce-`.

```
turso db config allow-rules set my-db --aws-vpc vpce-0fe6c8807461bba49

```

### Combine IP and VPC Rules

 Both lists may be configured by one command. A connection has to meet both conditions.

```
turso db config allow-rules set my-db \
  --ip 10.0.0.0/8 \
  --aws-vpc vpce-0fe6c8807461bba49

```

 If a later command includes only `--ip`, the VPC list stays as-is; the reverse is also true:

```
# Add a new IP without touching the VPC list
turso db config allow-rules set my-db --ip 198.51.100.5

```

### Clear Rules

 Delete every allow rule so connections are open from any source:

```
turso db config allow-rules clear my-db

```

 Empty one list without modifying the other list:

```
# Clear only the IP list
turso db config allow-rules clear my-db --ips

# Clear only the VPC list
turso db config allow-rules clear my-db --aws-vpcs

```

### API

 Manage allow rules with the database [configuration endpoint](https://docs.turso.tech/api-reference/databases/update-configuration). Include `allowed_ips` and/or `allowed_aws_vpc_ids` in the request body:

```
curl -L -X PATCH 'https://api.turso.tech/v1/organizations/{organizationSlug}/databases/{databaseName}/configuration' \
  -H 'Authorization: Bearer TOKEN' \
  -H 'Content-Type: application/json' \
  -d '{
    "allowed_ips": ["203.0.113.7", "10.0.0.0/8"],
    "allowed_aws_vpc_ids": ["vpce-0fe6c8807461bba49"]
  }'

```

 To empty a list, send an explicit empty array:

```
curl -L -X PATCH 'https://api.turso.tech/v1/organizations/{organizationSlug}/databases/{databaseName}/configuration' \
  -H 'Authorization: Bearer TOKEN' \
  -H 'Content-Type: application/json' \
  -d '{
    "allowed_ips": [],
    "allowed_aws_vpc_ids": []
  }'

```

 Leave a field out of the request body to keep that list as it is.

## Short term and long term guarantees

These durability guarantees apply on Turso Cloud for users who registered or upgraded **after March 17, 2025**:
|  | Durability | Added commit latency |
| --- | --- | --- |
| **Free** | 99.999999999% | up to 100ms |
| **Developer** | 99.999999999% | up to 50ms |
| **Scaler** | 99.999999999% | up to 25ms |
| **Pro and above** | 99.999999999% | up to 10ms |

 Across every Turso AWS region, the platform uses a diskless design backed by S3-Express One Zone and S3. Commits are acknowledged only after data is durably written to S3 or S3-express. Compute nodes are disposable, while local disks only serve as cache. That model lets Turso Cloud offer durability comparable to AWS S3 storage. Pro and Enterprise customers can point the service at their own S3 / S3-express buckets, keeping data within their infrastructure. The roadmap also includes running compute inside the customer's account for a full BYOC setup.

### Short term and long term guarantees

 A SQLite-compatible database generally has two storage parts: the Write-Ahead Log, or WAL, for recent commits, and the database file. At intervals, WAL data is checkpointed into the main database file. Turso Cloud places fresh commits in S3-express One Zone before acknowledging them to the application. Later, data is merged into the main database file and stored on S3. Under the AWS service agreement, both storage technologies carry a 99.999999999% durability guarantee.

### Commit latency

 Writing to object storage is expensive, which is one reason S3-backed designs are less common for transactional workloads than analytical ones. Turso Cloud's multi-tenant architecture batches commits across many databases to spread that cost. Commits are also grouped by time window, which produces the added commit latency shown in Table 1. That latency is a per-commit ceiling, not the expected delay for every commit. Consider this scenario:
- A first commit (c1) for database d1 at the start of a 10ms commit window waits the whole 10ms
- A commit (c1) for database d2 arriving 4ms into the window waits only the final 6ms
- A second commit (c2) for database d1 that lands at the window boundary receives no added latency

### Testing methodology

 Turso Cloud server was built to use Deterministic Simulation Testing from its first design. DST automatically exercises code with many randomized input sets; when a failure appears, it emits a seed that recreates the issue and its preconditions. Alongside the simulator, Turso uses the [Antithesis](https://antithesis.com) full-system Deterministic Simulation Hypervisor to cover interactions with external systems such as AWS S3 and S3-Express One Zone. Together, the DST-based server and Antithesis can compress years of rare scenario combinations into hours of testing, helping verify that the system follows its specification.

## AI Agents

As part of SOC2 standards, Turso Cloud encrypts every database at rest at the volume level. See [our trust center](https://trust.turso.tech) for details. Pro and Enterprise customers can also use a customer-supplied key through Bring-Your-Own-Key Encryption, or BYOK. BYOK Encryption is applied at the server level so data remains unavailable without the key. Separate databases can use separate keys, which lets you isolate customer databases from one another. When customers supply those keys themselves, BYOK Encryption prevents even your organization from reading their contents. For BYOK data-at-rest protection, Turso Cloud relies on AEAD algorithms that are standard in the industry. Database pages are protected independently so integrity and confidentiality are enforced page by page across environments. BYOK encryption is offered on Pro and [Enterprise](https://tur.so/turso-enterprise-chat) plans. **You hold the keys.** Each database can use a unique key controlled only by you. Turso never sees or stores your encryption keys. Encryption works with both remote queries and sync.
- **Page-level encryption**: Every 4 KiB page is encrypted separately with its own nonce
- **AEAD algorithms**: Works with AEGIS-256, AES-GCM, and ChaCha20Poly1305
- **Data integrity**: Authentication tags are built in to detect tampering and corruption
- **In-memory keys**: Encryption keys are kept off disk
 Turso Cloud with BYOK encryption fits these scenarios:

### AI Agents

Operate trusted agent fleets that handle personal private data

### Fintech Applications

Satisfy regulatory obligations for sensitive financial data

### Privacy-Focused Products

Earn user confidence through strong data protection

### Healthcare Apps

Safeguard patient data while meeting HIPAA compliance

### AI/ML Applications

Protect training data and model outputs

### What’s Encrypted

### Encrypted

- The database file and Write-Ahead Log (WAL) file, whether on disk or on S3.

### Generate an Encryption Key

 Create a secure encryption key in base64 format. Required key size depends on the selected cipher:
-  256-bit (32 bytes)
-  128-bit (16 bytes)
For AEGIS-256 variants and AES-256-GCM:

```
openssl rand -base64 32

```

Output example:

```
YUfkdsD2SJe4KzTB8n6CjwCgs1YIW6k1xxMEguRuI5s=

```

For AEGIS-128 variants and AES-128-GCM:

```
openssl rand -base64 16

```

Output example:

```
XG1iWMFBFYEYrmYxjWDf5w==

```

 **Store your key securely!** Losing the encryption key means the encrypted database cannot be opened. Turso Cloud never stores keys anywhere.

### Create an Encrypted Database

 1

Launch with encryption

When creating the database, pass `--remote-encryption-key` and `--remote-encryption-cipher` with the chosen key and cipher:

```
turso db create my-secret-db --remote-encryption-key "YOUR_SECRET_KEY" --remote-encryption-cipher aegis128l

```

Replace `YOUR_SECRET_KEY` with the generated key. Instead of passing the flag every time, you may set the `TURSO_DB_REMOTE_ENCRYPTION_KEY` environment variable.2

Create and insert data

Start the shell with the `--remote-encryption-key` flag, then create a table and insert data:

```
turso db shell my-secret-db --remote-encryption-key "YOUR_SECRET_KEY"

```

```
CREATE TABLE secrets (id INT, data TEXT);
INSERT INTO secrets VALUES (1, 'sensitive information');
INSERT INTO secrets VALUES (2, 'confidential data');

```

3

Verify encryption

Leave the shell by typing `.quit`, then try opening the database without the key:

```
turso db shell my-secret-db

```

The data will not be accessible because the cloud copy is encrypted.

### Upload Existing Database

 For an existing encrypted database upload, include the cipher and key flags at creation time:

```
turso db create my-secret-db --remote-encryption-key "YOUR_SECRET_KEY" --remote-encryption-cipher aegis128l --from-file my-db.db

```

### Branching and Point-in-Time Recovery (PITR)

 Encrypted databases work with [branching](https://docs.turso.tech/features/branching) and [point-in-time recovery (PITR)](https://docs.turso.tech/features/point-in-time-recovery). A new branch keeps the same cipher algorithm and encryption key. When creating it, provide the same encryption key used by the parent database:

```
turso db create my-secret-db-fork --remote-encryption-key "YOUR_SECRET_KEY" --from-db my-secret-db

```

### Rekeying

 Rekeying is not available yet. To change the cipher or key, export the database and create a replacement:

```
turso db export my-secret-db --remote-encryption-key "YOUR_SECRET_KEY"
turso db create my-new-secret-db --remote-encryption-key "YOUR_NEW_SECRET_KEY" --remote-encryption-cipher aegis256 --from-file my-secret-db.db

```

### Supported Ciphers

 Turso Cloud offers several variants across three AEAD encryption algorithms, with trade-offs in performance and compatibility.

### AES-GCM

 NIST-approved standard suited to compliance requirements. It is broadly supported across industries.
| Cipher | Key Size | Cipher Code | Use Case |
| --- | --- | --- | --- |
| **AES-128-GCM** | 128-bit (16 bytes) | `aes128gcm` | Compliance with 128-bit requirements |
| **AES-256-GCM** | 256-bit (32 bytes) | `aes256gcm` | Maximum security for compliance |

### ChaCha20-Poly1305

| Cipher | Key Size | Cipher Code | Use Case |
| --- | --- | --- | --- |
| **ChaCha20-Poly1305** | 256-bit (32 bytes) | `chacha20poly1305` | Alternative for AES |

### AEGIS

 Modern high-performance cipher family tuned for speed. **Recommended for most use cases.**
| Cipher | Key Size | Cipher Code | Use Case |
| --- | --- | --- | --- |
| **AEGIS-128L** | 128-bit (16 bytes) | `aegis128l` | Balanced performance, 128-bit security |
| **AEGIS-128X2** | 128-bit (16 bytes) | `aegis128x2` | 2x parallel processing |
| **AEGIS-128X4** | 128-bit (16 bytes) | `aegis128x4` | 4x parallel processing, maximum speed |
| **AEGIS-256** | 256-bit (32 bytes) | `aegis256` | Balanced 256-bit security |
| **AEGIS-256X2** | 256-bit (32 bytes) | `aegis256x2` | 2x parallel processing, 256-bit |
| **AEGIS-256X4** | 256-bit (32 bytes) | `aegis256x4` | 4x parallel, maximum speed & security |

 **Choosing a cipher:**
- Use `aegis128l` for 128 bit encryption, and choose `aegis256` for 256 bit encryption (default recommendation)
- For AES, choose `aes128gcm` for 128 bit encryption; use `aes256gcm` or `chacha20poly1305` for 256 bit encryption

### Future Work

 Future encryption features in development:
- **Encrypt existing databases**: Convert unencrypted databases into encrypted format
- **Key rotation**: Update encryption keys without data loss

## Limitations

### Limitations

 Turso Cloud supports most SQLite features, with a few known limitations, especially for migrations from vanilla SQLite.

### Pragma statements

 Some SQLite pragma statements either work differently on Turso Cloud or are unsupported.
| Pragma | Notes |
| --- | --- |
| `user_version` | Read-only on Turso Cloud. Track schema migrations with a `_schema_version` table instead. |
| `application_id` | Read-only on Turso Cloud. Store application identifiers in a `_metadata` table instead. |
| `busy_timeout` | Not supported. Turso Cloud handles concurrency internally, so a busy timeout setting changes nothing. |
| `journal_mode` | Not supported. Turso Cloud handles journaling internally and prevents journal mode changes. |

 If `PRAGMA user_version` currently tracks schema migrations in vanilla SQLite, move to a table-based method on Turso Cloud:

```
CREATE TABLE IF NOT EXISTS _schema_version (version INTEGER NOT NULL);
INSERT INTO _schema_version (version) VALUES (0);

```

## Preparing to Migrate

This guide explains how to move an existing SQLite database to Turso Cloud. You can use either the Turso CLI or the Platform API. (Figure: Migrate to Turso)

### Preparing to Migrate

 Before importing a SQLite database into Turso, verify that it meets Turso requirements. In particular, the SQLite database should use WAL (Write-Ahead Logging) mode. 1

Open your SQLite database

Open the database with the SQLite command-line tool or another SQLite client:

```
sqlite3 path/to/your/database.db

```

2

Set WAL journal mode

Use this command to change the database into WAL mode:

```
PRAGMA journal_mode='wal';

```

A `wal` result confirms that the change succeeded.3

Checkpoint and truncate the WAL file

Run a checkpoint so pending changes reach the main database file, then truncate the WAL file:

```
PRAGMA wal_checkpoint(truncate);

```

4

Verify the journal mode

Verify that the database is currently in WAL mode:

```
PRAGMA journal_mode;

```

This should return `wal`.5

Close the database

Exit the SQLite shell:

```
.exit

```

The database is ready to migrate to Turso.

### Using the CLI

 Use the Turso CLI to create a new database from a local SQLite file: 1

Install the Turso CLI

Confirm the [Turso CLI](https://docs.turso.tech/cli/introduction) is installed and that you are authenticated.2

Import your SQLite Database

Use the `db import` command to import the existing SQLite database file:

```
turso db import ~/path/to/my-database.db

```

The database name comes from the file name without the .db extension, and the import includes tables, data, and schema.3

Connect to your database

You can then connect to the database with the shell:

```
turso db shell <database-name>

```

### Using the Platform API

 The Platform API is another option for migrating an existing SQLite database: 1

Sign up or log in with the Turso CLI

If it is not already installed, [install the Turso CLI](https://docs.turso.tech/cli/installation).

```
turso auth signup

```

2

Create a new Platform API Token

Next, create a new API Token with the Turso CLI:

```
turso auth api-tokens mint quickstart

```

Save the token somewhere secure. You will need it when creating a database and a database token.3

Get your account or organization slug

The Platform API works with either a personal account or an organization. Use the Turso CLI to obtain the `slug` for the account or organization:

```
turso org list

```

4

Create a Database for Import

Start by creating a database prepared for an import:

```
curl -X POST "https://api.turso.tech/v1/organizations/{organizationSlug}/databases" \
  -L \
  -H "Authorization: Bearer TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
      "name": "new-database",
      "group": "default",
      "seed": { "type": "database_upload" }
  }'

```

The `seed` parameter containing `"type": "database_upload"` signals that a database file upload will follow. If the upload is not completed, this database still counts against your quota and remains unusable.5

Create a Database Token

Create an authentication token for the database:

```
curl -X POST "https://api.turso.tech/v1/organizations/{organizationSlug}/databases/{databaseName}/auth/tokens" \
  -L \
  -H "Authorization: Bearer TOKEN"

```

Use this token to authenticate the upload request and later database requests.6

Upload Your SQLite Database

Last, upload the SQLite database file:

```
curl -X POST "https://{databaseName}-{organizationSlug}.turso.io/v1/upload" \
  -H "Authorization: Bearer DATABASE_TOKEN" \
  --data-binary @/path/to/your/database.db

```

The `Authorization` header must use the database token created in the previous step, not the Platform API token. You can then connect to the new Turso database with any Turso client library.

### TypeScript / JS

### Rust

### Go

### Python

### Android

### Swift

### PHP

### Ruby

### Ruby on Rails

### C

### libSQL Remote Protocol
