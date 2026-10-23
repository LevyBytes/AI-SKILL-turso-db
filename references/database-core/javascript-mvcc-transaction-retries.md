# JavaScript MVCC transaction retries

[Context index](embedded-database-mvcc-and-encryption.md) · [Setup and preceding context](mvcc-setup-and-conflict-handling.md) · [Previous phase](rust-mvcc-transaction-retries.md)

Install `@tursodatabase/database` and run this as an ES module with Node.js. Each writer has its own connection. Conflicts and busy errors retry up to 100 times; other failures propagate after all writers finish.

The complete program is in the [upstream source](https://raw.githubusercontent.com/tursodatabase/turso/0687b18d5275ff3c8383b48479394e65e8cc0ec2/examples/javascript/concurrent-writes/index.mjs).

Error-message matching follows the upstream example; prefer typed error codes where the installed binding exposes them. The generated temporary directory belongs solely to this example.
