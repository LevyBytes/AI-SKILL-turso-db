# Cloud durability and commit latency

[Context index](access-durability-encryption-and-migration.md)

## Short term and long term guarantees

These durability guarantees apply on Turso Cloud for users who registered or upgraded **after March 17, 2025**:

|  | Durability | Added commit latency |
| --- | --- | --- |
| **Free** | 99.999999999% | up to 100ms |
| **Developer** | 99.999999999% | up to 50ms |
| **Scaler** | 99.999999999% | up to 25ms |
| **Pro and above** | 99.999999999% | up to 10ms |

 Across every Turso AWS region, the platform uses a diskless design backed by S3-Express One Zone and S3. Commits are acknowledged only after data is durably written to S3 or S3-express. Compute nodes are disposable, while local disks only serve as cache. That model lets Turso Cloud offer durability comparable to AWS S3 storage. Pro and Enterprise customers can point the service at their own S3 / S3-express buckets, keeping data within their infrastructure. The roadmap also includes running compute inside the customer's account for a full BYOC setup.

### Short term and long term guarantees

 A SQLite-compatible database generally has two storage parts: the Write-Ahead Log, or WAL, for recent commits, and the database file. At intervals, WAL data is checkpointed into the main database file. Turso Cloud places fresh commits in S3-express One Zone before acknowledging them to the application. Later, data is merged into the main database file and stored on S3. Under the AWS service agreement, both storage technologies carry a 99.999999999% durability guarantee.

### Commit latency

 Writing to object storage is expensive, which is one reason S3-backed designs are less common for transactional workloads than analytical ones. Turso Cloud's multi-tenant architecture batches commits across many databases to spread that cost. Commits are also grouped by time window, which produces the added commit latency shown in Table 1. That latency is a per-commit ceiling, not the expected delay for every commit. Consider this scenario:
- A first commit (c1) for database d1 at the start of a 10ms commit window waits the whole 10ms
- A commit (c1) for database d2 arriving 4ms into the window waits only the final 6ms
- A second commit (c2) for database d1 that lands at the window boundary receives no added latency

### Testing methodology

 Turso Cloud server was built to use Deterministic Simulation Testing from its first design. DST automatically exercises code with many randomized input sets; when a failure appears, it emits a seed that recreates the issue and its preconditions. Alongside the simulator, Turso uses the [Antithesis](https://antithesis.com) full-system Deterministic Simulation Hypervisor to cover interactions with external systems such as AWS S3 and S3-Express One Zone. Together, the DST-based server and Antithesis can compress years of rare scenario combinations into hours of testing, helping verify that the system follows its specification.
