# Python MVCC transaction retries

Install the Turso Python binding. Each thread owns its connection; the main coroutine waits for every worker before removing its temporary database directory. Retryable errors have a 100-attempt limit.

```python
import asyncio
import os
import random
import tempfile
import time
import turso


def is_retryable(error):
    message = str(error).lower()
    return "conflict" in message or "busy" in message


def write_worker(db_path):
    value = random.randint(1, 100)
    conn = turso.connect(db_path)
    try:
        conn.execute("PRAGMA journal_mode = 'mvcc'").fetchone()
        for attempt in range(100):
            try:
                conn.execute("BEGIN CONCURRENT")
                conn.execute("INSERT INTO hits VALUES (?)", (value,))
                conn.execute("COMMIT")
                return value
            except Exception as error:
                try:
                    conn.execute("ROLLBACK")
                except Exception:
                    pass
                if not is_retryable(error) or attempt == 99:
                    raise
                time.sleep(0.001)
    finally:
        conn.close()


async def main():
    with tempfile.TemporaryDirectory(prefix="turso-mvcc-") as directory:
        db_path = os.path.join(directory, "hits.db")
        setup = turso.connect(db_path)
        try:
            setup.execute("PRAGMA journal_mode = 'mvcc'").fetchone()
            setup.execute("CREATE TABLE hits (val INTEGER)")
            setup.commit()
        finally:
            setup.close()
        tasks = [asyncio.to_thread(write_worker, db_path) for _ in range(16)]
        results = await asyncio.gather(*tasks, return_exceptions=True)
        for result in results:
            if isinstance(result, BaseException):
                raise result
        final = turso.connect(db_path)
        try:
            final.execute("PRAGMA journal_mode = 'mvcc'").fetchone()
            row = final.execute("SELECT COUNT(*) FROM hits").fetchone()
            print(f"total rows: {row[0]}")
        finally:
            final.close()


if __name__ == "__main__":
    asyncio.run(main())
```

The example uses upstream message-based error classification. Pin and check the installed binding's error API before relying on it in a production retry policy.
