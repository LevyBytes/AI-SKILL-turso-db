# Concurrent Writes, Encryption, Quickstart

> Database Core reference. Original prose; identifiers preserved verbatim.

## Overview

Covers: Enable MVCC; Fintech Applications; Experimental Features.

## Enable MVCC

With the standard Turso settings, write access is limited to a single connection at once. Enabling MVCC (Multi-Version Concurrency Control) lets several connections perform writes at the same time. When two transactions change identical data, one transaction gets a conflict error and needs to roll back before retrying.

### Enable MVCC

 Configure the journal mode through your database connection:

```
PRAGMA journal_mode = 'mvcc';

```

### BEGIN CONCURRENT

 Start a transaction with `BEGIN CONCURRENT` rather than `BEGIN` when you want other writers to continue running concurrently:

```
BEGIN CONCURRENT;
-- your writes here
COMMIT;

```

 When two transactions affect identical rows, one gets a conflict error and needs to roll back before trying again. Writes against separate data can continue without conflicts.

### Handling Conflicts

 Your application needs to identify conflict errors and rerun the transaction.

### Example

-  Rust
-  JavaScript
-  Python

```
fn is_retryable(e: &Error) -> bool {
    matches!(e, Error::Busy(_) | Error::BusySnapshot(_))
        || matches!(e, Error::Error(msg) if msg.contains("conflict"))
}

#[tokio::main]
async fn main() -> Result<(), Error> {
    let tmp = NamedTempFile::new().expect("failed to create temp file");
    let db = Builder::new_local(tmp.path().to_str().unwrap())
        .build()
        .await?;

    let conn = db.connect()?;
    conn.pragma_update("journal_mode", "'mvcc'").await?;
    conn.execute("CREATE TABLE hits (val INTEGER)", ()).await?;

    let mut handles = Vec::new();
    for _ in 0..16 {
        let db = db.clone();
        handles.push(tokio::spawn(async move {
            let val = rand::rng().random_range(1..=100);
            let conn = db.connect()?;
            loop {
                conn.execute("BEGIN CONCURRENT", ()).await?;
                let result = conn
                    .execute(&format!("INSERT INTO hits VALUES ({val})"), ())
                    .await
                    .and(conn.execute("COMMIT", ()).await);
                match result {
                    Ok(_) => return Ok::<_, Error>(val),
                    Err(ref e) if is_retryable(e) => {
                        let _ = conn.execute("ROLLBACK", ()).await;
                        tokio::task::yield_now().await;
                    }
                    Err(e) => {
                        let _ = conn.execute("ROLLBACK", ()).await;
                        return Err(e);
                    }
                }
            }
        }));
    }

    for handle in handles {
        handle.await.expect("task panicked")?;
    }

    Ok(())
}

```

```
const DB_PATH = join(mkdtempSync(join(tmpdir(), "turso-mvcc-")), "hits.db");

function isRetryable(err) {
  const msg = (err?.message ?? "").toLowerCase();
  return msg.includes("conflict") || msg.includes("busy");
}

async function writeWorker() {
  const val = Math.floor(Math.random() * 100) + 1;
  const db = await connect(DB_PATH);
  try {
    await db.exec("PRAGMA journal_mode = 'mvcc'");
    while (true) {
      await db.exec("BEGIN CONCURRENT");
      try {
        await db.exec(`INSERT INTO hits VALUES (${val})`);
        await db.exec("COMMIT");
        return val;
      } catch (err) {
        try { await db.exec("ROLLBACK"); } catch (_) {}
        if (!isRetryable(err)) throw err;
        await new Promise((r) => setImmediate(r));
      }
    }
  } finally {
    await db.close();
  }
}

const setup = await connect(DB_PATH);
await setup.exec("PRAGMA journal_mode = 'mvcc'");
await setup.exec("CREATE TABLE hits (val INTEGER)");
await setup.close();

await Promise.all(Array.from({ length: 16 }, () => writeWorker()));

```

```
def is_retryable(e: Exception) -> bool:
    msg = str(e).lower()
    return "conflict" in msg or "busy" in msg

def write_worker(db_path: str) -> int:
    val = random.randint(1, 100)
    conn = turso.connect(db_path)
    try:
        conn.execute("PRAGMA journal_mode = 'mvcc'").fetchone()
        while True:
            conn.execute("BEGIN CONCURRENT")
            try:
                conn.execute("INSERT INTO hits VALUES (?)", (val,))
                conn.execute("COMMIT")
                return val
            except Exception as e:
                try:
                    conn.execute("ROLLBACK")
                except Exception:
                    pass
                if not is_retryable(e):
                    raise
    finally:
        conn.close()

async def main() -> None:
    with tempfile.NamedTemporaryFile(suffix=".db", delete=False) as tmp:
        db_path = tmp.name

    setup = turso.connect(db_path)
    setup.execute("PRAGMA journal_mode = 'mvcc'").fetchone()
    setup.execute("CREATE TABLE hits (val INTEGER)")
    setup.commit()
    setup.close()

    tasks = [asyncio.to_thread(write_worker, db_path) for _ in range(16)]
    await asyncio.gather(*tasks)

asyncio.run(main())

```

 See the complete example: [Rust](https://github.com/tursodatabase/turso/blob/main/bindings/rust/examples/concurrent_writes.rs) [JavaScript](https://github.com/tursodatabase/turso/blob/main/examples/javascript/concurrent-writes/index.mjs) [Python](https://github.com/tursodatabase/turso/blob/main/examples/python/concurrent_writes.py)

## Fintech Applications

Turso Database includes built-in encryption for data at rest with industry-standard AEAD algorithms. It encrypts each database page separately, providing integrity checks and protection in every environment.
- **Page-level encryption**: Every 4 KiB page is encrypted separately and receives its own unique nonce
- **AEAD algorithms**: Includes AEGIS-256 (fast, modern) and AES-GCM (NIST-approved)
- **Data integrity**: Authentication tags are built in to detect tampering and corruption
- **High performance**: Read overhead can be as low as 6%, with write overhead from 14%
- **In-memory keys**: Encryption keys never get written to disk
 Turso Database encryption fits use cases such as:

### Fintech Applications

Satisfy regulatory obligations for sensitive financial data

### Privacy-Focused Products

Increase user trust through strong data protection

### Healthcare Apps

Safeguard patient data while supporting HIPAA compliance

### AI/ML Applications

Protect training data and model outputs

### What’s Encrypted

### Encrypted

- Every database page containing your data - The database file - Write-Ahead Log (WAL) file

### Not Encrypted

- The database header (first 100 bytes)

### Generate an Encryption Key

 Create a secure encryption key as hexadecimal text. The required key length is determined by the cipher you select:
-  256-bit (32 bytes)
-  128-bit (16 bytes)
For AES-256-GCM and AEGIS-256 variants:

```
openssl rand -hex 32

```

Output example:

```
2d7a30108d3eb3e45c90a732041fe54778bdcf707c76749fab7da335d1b39c1d

```

For AES-128-GCM and AEGIS-128 variants:

```
openssl rand -hex 16

```

Output example:

```
5f3e2a8c9b1d4f6e7a2c8d4b9e1f3a6c

```

 **Store your key securely!** Losing the encryption key means you cannot open the encrypted database again. Keys are never stored on disk.

### Create an Encrypted Database

 1

Launch with encryption

Pass `--experimental-encryption`, then provide the cipher and key through the connection URI:

```
tursodb --experimental-encryption "file:encrypted.db?cipher=aegis256&hexkey=YOUR_HEX_KEY"

```

Substitute `YOUR_HEX_KEY` with the key generated earlier.2

Create and insert data

After the interactive shell opens, create a table and add some data:

```
CREATE TABLE secrets (id INT, data TEXT);
INSERT INTO secrets VALUES (1, 'sensitive information');
INSERT INTO secrets VALUES (2, 'confidential data');

```

3

Verify encryption

Leave the shell by typing `.quit`, then attempt to open the database without providing the key:

```
tursodb encrypted.db

```

The data will not be accessible because the database is encrypted on disk.

### Open an Encrypted Database

 When opening an encrypted database that already exists, use the identical cipher and key from creation:

```
tursodb --experimental-encryption "file:encrypted.db?cipher=aegis256&hexkey=YOUR_HEX_KEY"

```

 Then query your data:

```
SELECT * FROM secrets;

```

### Supported Ciphers

 Turso Database offers several variants across two AEAD encryption algorithms, with trade-offs in compatibility and performance.

### AES-GCM

 NIST-approved option suitable for compliance needs. Supported broadly across many industries.
| Cipher | Key Size | URI Value | Use Case |
| --- | --- | --- | --- |
| **AES-128-GCM** | 128-bit (16 bytes) | `aes128gcm` | Compliance with 128-bit requirements |
| **AES-256-GCM** | 256-bit (32 bytes) | `aes256gcm` | Maximum security for compliance |

### AEGIS

 Modern cipher family built for high performance and speed. **Recommended for most use cases.**
| Cipher | Key Size | URI Value | Use Case |
| --- | --- | --- | --- |
| **AEGIS-128L** | 128-bit (16 bytes) | `aegis128l` | Balanced performance, 128-bit security |
| **AEGIS-128X2** | 128-bit (16 bytes) | `aegis128x2` | 2x parallel processing |
| **AEGIS-128X4** | 128-bit (16 bytes) | `aegis128x4` | 4x parallel processing, maximum speed |
| **AEGIS-256** | 256-bit (32 bytes) | `aegis256` | Balanced 256-bit security |
| **AEGIS-256X2** | 256-bit (32 bytes) | `aegis256x2` | 2x parallel processing, 256-bit |
| **AEGIS-256X4** | 256-bit (32 bytes) | `aegis256x4` | 4x parallel, maximum speed & security |

 **Choosing a cipher:**
- Choose `aegis128L` for 128 bit encryption, or `aegis256` for 256 bit encryption (default recommendation)
- Select other AEGIS variants when supported by your hardware: `aegis128X2` `aegis128X4` `aegis256x2` `aegis256x4`
- For AES, select `aes128gcm` for 128 bit encryption or `aes256gcm` for 256 bit encryption

### URI Parameters

 Use this URI format when creating or opening an encrypted database:

```
file:database.db?cipher=CIPHER&hexkey=HEX_KEY

```

| Parameter | Required | Description | Values |
| --- | --- | --- | --- |
| `cipher` | Yes | Encryption algorithm | See Supported Ciphers |
| `hexkey` | Yes | Encryption key in hex format | 32-character hex (128-bit) or 64-character hex (256-bit) |

 **Example URIs:**

```
# AEGIS-256 with 256-bit key
file:encrypted.db?cipher=aegis256&hexkey=2d7a30108d3eb3e45c90a732041fe54778bdcf707c76749fab7da335d1b39c1d

# AES-128-GCM with 128-bit key
file:encrypted.db?cipher=aes128gcm&hexkey=5f3e2a8c9b1d4f6e7a2c8d4b9e1f3a6c

# AEGIS-256X4 (max performance) with 256-bit key
file:encrypted.db?cipher=aegis256x4&hexkey=2d7a30108d3eb3e45c90a732041fe54778bdcf707c76749fab7da335d1b39c1d

```

### Performance

 Encryption adds only a small amount of overhead, particularly with smaller databases:
- **Read operations**: ~6% overhead with AEGIS-256
- **Write operations**: ~14% overhead with AEGIS-256
- **Overall**: Mixed workloads see about ~1-3% overhead in total time
 For deeper benchmark details, read the [encryption announcement blog post](https://turso.tech/blog/introducing-fast-native-encryption-in-turso-database).

### Coming Soon

 Future encryption features in development:
- **Key derivation from passphrases**: Allow memorable passphrases instead of requiring raw keys
- **Encrypt existing databases**: Convert unencrypted databases into encrypted format
- **Key rotation**: Update encryption keys without data loss
- **`ATTACH` support**: Use several encrypted databases at the same time

## Experimental Features

1

Install

-  macOS / Linux
-  Windows

```
curl --proto '=https' --tlsv1.2 -LsSf https://github.com/tursodatabase/turso/releases/latest/download/turso_cli-installer.sh | sh

```

Execute this command from PowerShell:

```
irm https://github.com/tursodatabase/turso/releases/latest/download/turso_cli-installer.ps1 | iex

```

As another option, [download the Windows executable](https://github.com/tursodatabase/turso/releases/latest) and place it on your PATH.2

Launch

Then launch the interactive shell:

```
tursodb

```

That starts the Turso interactive shell, where SQL statements can be run:

```
Turso
Enter ".help" for usage hints.
Connected to a transient in-memory database.
Use ".open FILENAME" to reopen on a persistent database
turso>

```

3

Execute

At this point, SQL statements can be executed in the interactive shell:

Create a table

Create a table for users:

```
CREATE TABLE users (id INT, username TEXT);

```

Insert data

Add some rows to the users table:

```
INSERT INTO users VALUES (1, 'alice');
INSERT INTO users VALUES (2, 'bob');

```

Query data

Read every user from the table:

```
SELECT * FROM users;

```

This will return:

```
1|alice
2|bob

```

### Experimental Features

 Experimental features can be enabled while starting `tursodb` with flags including `--experimental-encryption`, `--experimental-mvcc`, `--experimental-strict`, and `--experimental-views`. **Note:** These features are not ready for production, so avoid using them with critical data for now. The [Turso manual](https://github.com/tursodatabase/turso) lists the full set of available flags and options.
