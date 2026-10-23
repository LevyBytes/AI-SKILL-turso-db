# Python MVCC transaction retries

Install the Turso Python binding. Each thread owns its connection; the main coroutine waits for every worker before removing its temporary database directory. Retryable errors have a 100-attempt limit.

The complete program is in the [upstream source](https://raw.githubusercontent.com/tursodatabase/turso/0687b18d5275ff3c8383b48479394e65e8cc0ec2/examples/python/concurrent_writes.py). Correct these defects when adapting it:

- Run `BEGIN CONCURRENT` inside the retried `try`. In the source a busy error on `BEGIN` escapes without a rollback or a retry.
- Bound the retry loop, about 100 attempts, and sleep about a millisecond between attempts. The source's `while True` retries without limit or pause.
- Gather the workers with `return_exceptions=True` and re-raise the first failure after every thread has finished. Otherwise cleanup deletes the database while other threads still write to it.
- Close every connection in `finally`, and create the database inside `tempfile.TemporaryDirectory()` so cleanup removes every sidecar file, not only `-wal` and `-log`.

The example uses upstream message-based error classification. Pin and check the installed binding's error API before relying on it in a production retry policy.
