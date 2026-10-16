# Encryption coverage and key generation

[Context index](embedded-database-mvcc-and-encryption.md) · [Continue](encrypted-database-creation-and-ciphers.md)

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

- Every database page containing your data
- The database file
- Write-Ahead Log (WAL) file

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
