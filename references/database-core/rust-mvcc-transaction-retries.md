# Rust MVCC transaction retries

[Context index](embedded-database-mvcc-and-encryption.md) · [Setup and preceding context](mvcc-setup-and-conflict-handling.md) · [Continue](javascript-mvcc-transaction-retries.md)

Use the Turso Rust binding with `tokio` (macros and runtime), `rand` 0.9 and `tempfile` 3. Each writer uses its own connection. The retry budget is 100 attempts; an insert failure never proceeds to commit.

```rust
use rand::Rng;
use tempfile::NamedTempFile;
use turso::{Builder, Error};

fn is_retryable(e: &Error) -> bool {
    matches!(e, Error::Busy(_) | Error::BusySnapshot(_))
        || matches!(e, Error::Error(msg) if msg.contains("conflict"))
}

#[tokio::main]
async fn main() -> Result<(), Error> {
    let tmp = NamedTempFile::new().expect("failed to create temp file");
    let db = Builder::new_local(tmp.path().to_str().unwrap())
        .build()
        .await?;

    let conn = db.connect()?;
    conn.pragma_update("journal_mode", "'mvcc'").await?;
    conn.execute("CREATE TABLE hits (val INTEGER)", ()).await?;

    let mut handles = Vec::new();
    for _ in 0..16 {
        let db = db.clone();
        handles.push(tokio::spawn(async move {
            let val = rand::rng().random_range(1..=100);
            let conn = db.connect()?;
            for attempt in 0..100 {
                let result = async {
                    conn.execute("BEGIN CONCURRENT", ()).await?;
                    conn.execute("INSERT INTO hits VALUES (?)", [val]).await?;
                    conn.execute("COMMIT", ()).await
                }.await;
                match result {
                    Ok(_) => return Ok::<_, Error>(val),
                    Err(ref e) if is_retryable(e) && attempt < 99 => {
                        let _ = conn.execute("ROLLBACK", ()).await;
                        tokio::task::yield_now().await;
                    }
                    Err(e) => {
                        let _ = conn.execute("ROLLBACK", ()).await;
                        return Err(e);
                    }
                }
            }
            unreachable!("every final attempt returns")
        }));
    }

    let mut first_error = None;
    for handle in handles {
        match handle.await.expect("task panicked") {
            Ok(val) => println!("inserted val={val}"),
            Err(error) if first_error.is_none() => first_error = Some(error),
            Err(_) => {},
        }
    }
    if let Some(error) = first_error { return Err(error); }

    let mut rows = conn.query("SELECT COUNT(*) FROM hits", ()).await?;
    if let Some(row) = rows.next().await? {
        println!("total rows: {}", row.get::<i64>(0)?);
    }

    Ok(())
}
```
