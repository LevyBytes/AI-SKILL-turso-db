# Encrypted database creation upload and branching

[Context index](access-durability-encryption-and-migration.md) · [Setup and preceding context](byok-protection-and-key-generation.md) · [Continue](encryption-ciphers-and-key-sizes.md)

### Create an Encrypted Database
### Step 1: Launch with encryption

When creating the database, pass `--remote-encryption-key` and `--remote-encryption-cipher` with the chosen key and cipher:

```
turso db create my-secret-db --remote-encryption-key "YOUR_SECRET_KEY" --remote-encryption-cipher aegis128l

```

Replace `YOUR_SECRET_KEY` with the generated key. Instead of passing the flag every time, you may set the `TURSO_DB_REMOTE_ENCRYPTION_KEY` environment variable.

### Step 2: Create and insert data

Start the shell with the `--remote-encryption-key` flag, then create a table and insert data:

```
turso db shell my-secret-db --remote-encryption-key "YOUR_SECRET_KEY"

```

```
CREATE TABLE secrets (id INT, data TEXT);
INSERT INTO secrets VALUES (1, 'sensitive information');
INSERT INTO secrets VALUES (2, 'confidential data');

```

### Step 3: Verify encryption

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
