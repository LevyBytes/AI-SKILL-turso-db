# Python embedded replica selection

[Context index](python-and-ruby-sdk-workflows.md)

### Embedded Replicas (libsql)

 The `libsql` package can also use Embedded Replicas: reads happen from a local file, writes are forwarded to the cloud primary, and the replica receives the reflected changes. Embedded Replicas are production-supported. For new projects that require sync, Turso recommends `turso.sync`, where reads and writes both occur locally and synchronization is performed explicitly with `push()` / `pull()`. The [reference](https://docs.turso.tech/sdk/python/reference) covers Embedded Replicas, encryption, periodic sync, and related APIs.
