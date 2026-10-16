# Encrypted database creation and ciphers

[Context index](embedded-database-mvcc-and-encryption.md) · [Setup and preceding context](encryption-coverage-and-key-generation.md) · [Continue](encryption-uri-parameters-and-performance.md)

### Create an Encrypted Database

### Step 1: Launch with encryption

Pass `--experimental-encryption`, then provide the cipher and key through the connection URI:

```
tursodb --experimental-encryption "file:encrypted.db?cipher=aegis256&hexkey=YOUR_HEX_KEY"

```

Substitute `YOUR_HEX_KEY` with the key generated earlier.

### Step 2: Create and insert data

After the interactive shell opens, create a table and add some data:

```
CREATE TABLE secrets (id INT, data TEXT);
INSERT INTO secrets VALUES (1, 'sensitive information');
INSERT INTO secrets VALUES (2, 'confidential data');

```

### Step 3: Verify encryption

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
- Choose `aegis128l` for 128 bit encryption, or `aegis256` for 256 bit encryption (default recommendation)
- Select other AEGIS variants when supported by your hardware: `aegis128x2` `aegis128x4` `aegis256x2` `aegis256x4`
- For AES, select `aes128gcm` for 128 bit encryption or `aes256gcm` for 256 bit encryption
