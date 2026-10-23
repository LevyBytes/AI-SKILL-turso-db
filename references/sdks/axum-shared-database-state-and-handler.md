# Axum shared database state and handler

[Context index](rust-framework-and-connection-workflows.md) · [Setup and preceding context](axum-dependencies-and-database-query.md)

This example targets **Axum 0.6.20**, whose `Server::bind` API is used below. Use Tokio with macros/runtime support, libsql 0.9.30 and Serde with `derive`. Export the database credentials and create `users(id, name)` before serving requests.

The complete program is in the [upstream source](https://raw.githubusercontent.com/tursodatabase/turso-docs/fb78fbb00393f0a9eeab1f302a2bfa48c1725384/sdk/rust/guides/axum.mdx).

This example puts a shared `Database` instance in application state and uses it from the handler to run queries.
