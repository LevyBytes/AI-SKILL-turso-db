# Turso 0.5.0: added bindings and sync

Open when checking these changes in Turso 0.5.0, released 2026-03-04. These are historical release records, not current API availability guarantees.

[Release history](release-history.md) · [This release](release-0.5.0-index.md)

## Added

- Introduce JS and Python concurrent write examples (Avinash Sajjanshetty)
- perf/react-native: add c++ getAllRows() helper (Jussi Saurio)
- Introduce concurrent writes example in Rust (Avinash Sajjanshetty)
- Introduce SQLAlchemy dialect for Python bindings (Orthel Toralen)
- Introduce native TCL extension for in-process SQLite test harness (Ahmad Alhour)
- bindings/rust: Introduce statement and row column inspection APIs (Pekka Enberg)
- cli/sync: introduce read timeout to avoid deadlock on Windows (Orthel Toralen)
- bindings/rust: promote old multithreaded bug reproducers into mvcc regression tests (Jussi Saurio)
- feat: Introduce materialized views support to Rust bindings and test runner (Martin Mauch)
- javascript: Introduce support for Statement.reader property (Pekka Enberg)
- javascript: Fix implicit connect support (Pekka Enberg)
- Introduce busy timeout and Connector pattern for go bindings (Preston Thorpe)
- Introduce Namespace field for go-sync package (Preston Thorpe)
- Introduce support for local encryption in SDKs (Avinash Sajjanshetty)
- Introduce local encryption support for Go (Avinash Sajjanshetty)
- Introduce support for remote encryption in sync operations (Avinash Sajjanshetty)
- bindings/rust: support `prepare_cached` (Pere Diaz Bou)
- bindings/dotnet: implement IsDBNull check (Alex)
- bindings/java: Add transaction support (Kim Seon Woo)
