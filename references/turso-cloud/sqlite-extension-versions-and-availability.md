# SQLite extension versions and availability

[Context index](sqlite-extension-availability-reference.md)

Turso ships with extensions that add extra capabilities on top of SQLite. Some plans preload additional extensions. For vector functionality, prefer the native [libSQL vector datatype](https://docs.turso.tech/features/ai-and-embeddings) instead of enabling vector extensions.

### Preloaded Extensions

 These extensions are enabled by default and always available:

| Extension | Version | Description |
| --- | --- | --- |
| JSON | Built-in | Use JSON data from SQLite. |
| FTS5 | Built-in | Full-text search and indexing. |
| R*Tree | Built-in | Indexing and querying spatial data. |
| SQLean Crypto | 0.24.1 | Hashing, message digest, encoding, and decoding utilities. |
| SQLean Fuzzy | 0.24.1 | Fuzzy matching and phonetic string search. This is an improved fork of Spellfix1. |
| SQLean Math | 0.24.1 | Advanced mathematical calculations. |
| SQLean Stats | 0.24.1 | Common statistical functions with SQLite. |
| SQLean Text | 0.24.1 | SQLite string utilities such as reverse and split. |
| SQLean UUID | 0.24.1 | Partial support for UUIDs that follow RFC 4122. |

### Optional Extensions

 Databases on the Fly Cloud Provider under Pro & Enterprise also have these optional extensions enabled alongside the preloaded set.

| Extension | Version | Description |
| --- | --- | --- |
| SQLean Regexp | 0.24.1 | Regular expressions with SQLite. |
| sqlite-vec | 0.1.3 | The Vector Search successor to `sqlite-vss`. |
