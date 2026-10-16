# Go embedded replica encryption

[Context index](go-sdk-workflows.md) · [Setup and preceding context](go-embedded-replica-connection-and-sync.md)

### Encryption

 For new projects, choose `tursogo` for local encryption; it uses the Turso Database engine and provides stronger performance plus concurrent write support. To turn on encryption for a SQLite file, provide the encryption key value to the constructor:

```
encryptionKey := "SuperSecretKey"

connector, err := libsql.NewEmbeddedReplicaConnector(dbPath, primaryUrl,
    libsql.WithAuthToken(authToken),
    libsql.WithEncryption(encryptionKey),
)

```

 Encrypted databases look like raw bytes and are not readable as standard SQLite databases. Operations must go through the libSQL client; see [learn more](https://docs.turso.tech/libsql#encryption-at-rest).
