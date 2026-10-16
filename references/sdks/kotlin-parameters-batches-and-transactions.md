# Kotlin parameters batches and transactions

[Context index](kotlin-and-php-sdk-workflows.md) · [Setup and preceding context](kotlin-client-configuration-and-queries.md)

### Placeholders

 libSQL allows positional and named placeholders inside SQL statements: Positional

```
db.connect().use {
    it.query("SELECT * FROM users WHERE id = ?", 1)
}

```

 Named

```
db.connect().use {
    it.query("SELECT * FROM users WHERE id = :id", mapOf(":id" to 1))
}

```

### Batch Transactions

 `executeBatch` runs several SQL statements. Use an explicit transaction when the group of statements must succeed or fail together; the method name alone does not establish atomicity.

```
db.connect().use {
    it.executeBatch("""
      CREATE TABLE IF NOT EXISTS users (
        id INTEGER PRIMARY KEY,
        name TEXT NOT NULL
      );

      INSERT INTO users (name) VALUES ('Alice');
      INSERT INTO users (name) VALUES ('Bob');
    """.trimIndent())
}

```

### Transactions

 Interactive transactions in SQLite keep a sequence of read and write operations consistent within the transaction's scope. They let you decide when to commit or roll back changes while keeping that work isolated from other client activity.

```
db.connect().use {
    val tx = it.transaction();

    try {
        tx.execute("INSERT INTO users (name) VALUES (?)", "Iku")
        tx.execute("INSERT INTO users (name) VALUES (?)", "Iku 2")
        tx.commit()
    } catch (error: Exception) {
        tx.rollback()
        throw error
    }
}

```
