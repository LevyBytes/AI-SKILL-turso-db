# BYOK protection and key generation

[Context index](access-durability-encryption-and-migration.md) · [Continue](encrypted-database-creation-upload-and-branching.md)

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
