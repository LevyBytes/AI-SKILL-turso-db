# Turso 0.0.3: changes

Open when checking these changes in Turso 0.0.3, released 2024-08-01. These are historical release records, not current API availability guarantees.

[Release history](release-history.md) · [This release](release-0.0.3-index.md)

## Added

- First write path implementation. The write path remains non-transactional for now. (Pere Diaz Bou)
- Additional scalar functions: `unicode()` (Ethan Niser)
- Speed up point queries that use integer keys (Jussi Saurio)
## Fixed

- Add `ORDER BY` handling for nullable sort columns and qualified identifiers (Jussi Saurio)
- Prevent `.schema` command crashes in the CLI ([#212](https://github.com/tursodatabase/limbo/issues/212)) (Jussi Saurio)
