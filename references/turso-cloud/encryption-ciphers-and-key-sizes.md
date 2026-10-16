# Encryption ciphers and key sizes

[Context index](access-durability-encryption-and-migration.md) · [Setup and preceding context](byok-protection-and-key-generation.md) · [Previous phase](encrypted-database-creation-upload-and-branching.md)

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
