# Encryption uri parameters and performance

[Context index](embedded-database-mvcc-and-encryption.md) · [Setup and preceding context](encryption-coverage-and-key-generation.md) · [Previous phase](encrypted-database-creation-and-ciphers.md)

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
