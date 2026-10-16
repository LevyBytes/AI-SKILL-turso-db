# Pragma encryption and custom types

[Context index](multiprocess-and-pragma-reference.md) · [Setup and preceding context](pragma-database-metadata-and-page-sizes.md) · [Previous phase](pragma-change-capture-modes-and-records.md)

### cipher

 Configures the database encryption cipher.

```
PRAGMA cipher = 'aegis256';

```

 Supported ciphers:

| Cipher | Key Size | Description |
| --- | --- | --- |
| `aes128gcm` | 16 bytes | AES-128 in Galois/Counter Mode |
| `aes256gcm` | 32 bytes | AES-256 in Galois/Counter Mode |
| `aegis128l` | 16 bytes | AEGIS-128L |
| `aegis256` | 32 bytes | AEGIS-256 (recommended) |
| `aegis128x2` | 16 bytes | AEGIS-128 with 2x parallelization |
| `aegis128x4` | 16 bytes | AEGIS-128 with 4x parallelization |
| `aegis256x2` | 32 bytes | AEGIS-256 with 2x parallelization |
| `aegis256x4` | 32 bytes | AEGIS-256 with 4x parallelization |

### hexkey

 Configures the encryption key from a hexadecimal string.

```
PRAGMA hexkey = '2d7a30108d3eb3e45c90a732041fe54778bdcf707c76749fab7da335d1b39c1d';

```

#### Encryption Example

```
-- Set cipher and key before creating tables
PRAGMA cipher = 'aegis256';
PRAGMA hexkey = '2d7a30108d3eb3e45c90a732041fe54778bdcf707c76749fab7da335d1b39c1d';

CREATE TABLE secrets (id INTEGER PRIMARY KEY, data TEXT);
INSERT INTO secrets VALUES (1, 'sensitive data');

```

 You can also provide encryption parameters in the database URI:

```
file:database.db?cipher=aegis256&hexkey=2d7a30...

```

 Opening an existing encrypted database requires supplying both the cipher and key as URI parameters.

### Custom Types

 **Turso Extension**: Custom types are specific to Turso.

### list_types

 Outputs all available types, including built-in and custom types, along with their metadata.

```
PRAGMA list_types;
-- type    | parent | encode | decode | default | operators
-- INTEGER |        |        |        |         |
-- REAL    |        |        |        |         |
-- TEXT    |        |        |        |         |
-- BLOB    |        |        |        |         |
-- ANY     |        |        |        |         |

```

 For creating custom types, see [CREATE TYPE](https://docs.turso.tech/sql-reference/statements/create-type).

### See Also

- [Transactions](https://docs.turso.tech/sql-reference/statements/transactions) covers transaction control
- [CREATE TYPE](https://docs.turso.tech/sql-reference/statements/create-type) covers custom type definitions
- [Compatibility](https://docs.turso.tech/sql-reference/compatibility) lists all supported PRAGMAs
