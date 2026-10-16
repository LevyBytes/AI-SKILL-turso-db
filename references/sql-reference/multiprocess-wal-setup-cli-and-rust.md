# Multiprocess wal setup CLI and rust

[Context index](multiprocess-and-pragma-reference.md) · [Continue](multiprocess-bindings-concurrency-and-limits.md)

## Multi-Process Access

 Multi-process access remains **experimental**. Turso may alter both the on-disk coordination layout and the public API from one release to another. Treat that format as unsuitable for long-term storage across Turso versions. Normally, one Turso database file is opened by one OS process. Threads and connections inside that process may share the database safely, while a second process opening the same file is refused with a locking error. Multi-process access relaxes that rule. With the `multiprocess_wal` feature turned on, multiple processes may concurrently open the same `.db` file and use a shared memory file to coordinate WAL reads, writes, and checkpoints.

### When to Use It

 Turn on multi-process access for cases that require:
- Separate OS processes (workers, sidecars, CLI + embedded app) that must read and write the same database file without adding a server tier.
- Deployments with no downtime where the old process and replacement process temporarily share the same database.
- Process-per-connection layouts that want more isolation than threads provide.
 For applications that stay inside one process - including multi-threaded servers backed by a connection pool - the default mode is faster and needs no setup.

### How It Works

 After multi-process access is turned on, Turso places an extra sibling file alongside the database:

| File | Purpose |
| --- | --- |
| `mydb.db` | The database file (unchanged). |
| `mydb.db-wal` | The write-ahead log (unchanged). |
| `mydb.db-tshm` | Turso shared memory. Memory-mapped coordination file that records WAL state, active writer ownership, active checkpointer ownership, reader slots, and the shared page-to-frame index. |

 Each participating process uses mmap on the `.tshm` file and coordinates through:
- A **single-writer** slot. Across the cluster, only one process can hold the writer lock at a time.
- A **single-checkpointer** slot. Processes perform checkpointing one at a time.
- A limited pool of **reader slots**. Active read transactions pin WAL frames so concurrent writers cannot overwrite them and the checkpointer cannot reclaim them.
- A shared **frame index** that lets readers in any process map a page number to the newest WAL frame without starting a WAL scan at the beginning.
 Ownership transfer uses byte-range locks that work across processes on the `.tshm` file: OFD locks on Linux and `fcntl` on macOS. The mmap regions remain the fast path for reading shared metadata.

### Requirements

 Multi-process WAL can be used only when every requirement below is met:
-  **Platform**: A 64-bit Unix-like OS is required, including Linux, macOS, Android, and other supported Unix systems. On Windows, WASM, and 32-bit targets, Turso accepts the flag but ignores it, opening the database in single-process mode.
-  **IO backend**: The selected IO backend must provide shared WAL coordination. The default file-backed backends provide it; memory-only backends and some custom VFSes do not.
-  **Filesystem**: Store the database on a local filesystem with correct POSIX byte-range lock and mmap behavior. The filesystems below are **explicitly rejected** because shared mmap coordination is unsafe on them:

| Filesystem | Reason |
| --- | --- |
| NFS | Advisory lock semantics diverge across clients |
| CIFS / SMB2 | Same condition as above; mmap coherence cannot be guaranteed |
| CephFS | Distributed cache semantics |
| GFS2 | Cluster filesystem; not validated |
| Lustre | Distributed; not validated |
| OCFS2 | Cluster filesystem; not validated |
| AFS, CODA, NCP, 9P (v9fs) | Network/legacy filesystems whose mmap+lock semantics are unreliable |

 If `multiprocess_wal` is enabled and the database is opened on one of these filesystems, Turso returns `InvalidArgument`.
-  **Not in-memory**: `:memory:` and `file::memory:` paths fail because shared coordination needs a durable file.

### Enabling Multi-Process Access

### CLI

 Pass `--experimental-multiprocess-wal`:

```
tursodb --experimental-multiprocess-wal mydb.db

```

 Each `tursodb` (or SDK) process that opens `mydb.db` concurrently has to use the flag. Turso rejects mixed modes; see Mixing Modes.

### Rust SDK

```
// Inside an async function returning a compatible Result.
use turso::Builder;

let db = Builder::new_local("mydb.db")
    .experimental_multiprocess_wal(true)
    .build().await?;

```
