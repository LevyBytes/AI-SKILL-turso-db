# Partial sync bootstrap and segment size

[Context index](database-sync-workflows.md) · [Continue](partial-sync-page-prefetch.md)

## Modes

This setup relies on Turso Cloud for syncing local Turso databases, so it expects an existing account. Partial sync allows an app to open and query a database before the full file has been downloaded. When a query reaches data missing from the local copy, the client fetches the needed database pages from Turso Cloud on demand. For large databases, this can cut startup latency and network transfer while still working with the same push/pull methods as Turso's standard `sync` solution.
- Reads that touch data not yet present locally cause the needed pages to be fetched automatically.
- Writes continue to land locally first, then sync as logical statements.

### Modes

 At connection time, two bootstrap options determine the initial local contents:
- **Prefix bootstrap**: fetch the first N bytes from the database file.
  - A useful default when the initial local footprint should be small and predictable.

- **Query bootstrap**: fetch the pages reached by executing a server-side SQL query.
  - Best when only a focused working set should be hydrated, such as a single user's rows, small metadata tables, references, etc.

 In either mode, absent pages are still fetched lazily when needed.

### Prefix bootstrap

```
import { connect } from '@tursodatabase/sync';

const db = await connect({
  path: './app.db',
  url: 'libsql://...',
  authToken: process.env.TURSO_AUTH_TOKEN,
  partialSyncExperimental: {
    bootstrapStrategy: { kind: 'prefix', length: 128 * 1024 }, // 128 KiB
  },
});

```

### Query bootstrap

```
import { connect } from '@tursodatabase/sync';

const db = await connect({
  path: './app.db',
  url: 'libsql://...',
  authToken: process.env.TURSO_AUTH_TOKEN,
  partialSyncExperimental: {
    bootstrapStrategy: {
      kind: 'query',
      query: `SELECT * FROM messages WHERE user_id = 'u_123' LIMIT 100`,
    },
  },
});

```

### Optimizations

### Segment size (batched lazy reads)

Read page 7 → fetch segment 5-8 (3 new pages)

 When a client references a page that is not stored locally, partial sync retrieves it from the remote database on demand. The **segment size** setting reduces round-trips for these lazy reads: rather than asking for only one page, the client can fetch an entire *segment* of pages per request. This spreads network overhead across more data and preloads nearby pages that may be read soon. **How it works** Suppose your database has:
- `page_size = 4 KiB`
- `segment_size = 16 KiB`
 When a local query reaches page **6**, the client identifies the segment containing that page:
- 16 KiB segment = 4 pages
- Segment covering page 6 = pages **5-8**
 The client downloads all four pages with one request and saves them locally. Later reads for those pages need no extra network request. **Benefits**
- Fewer HTTP requests (one segment fetch vs. many single-page fetches)
- Faster hydration of hot ranges
- Higher performance when access patterns show spatial locality, such as range scans or index lookups
 **Default** `segment_size` defaults to **128 KiB**. On a 4 KiB page size, that normally covers 32 pages, balancing fewer requests against extra transferred data. Workloads that read clustered data, such as several adjacent rows, may run much faster with larger segments. Sparse or random-access workloads may do better with smaller segment sizes.

```
await connect({
  path: './app.db',
  url: process.env.TURSO_DATABASE_URL,
  authToken: process.env.TURSO_AUTH_TOKEN,
  partialSyncExperimental: {
    bootstrapStrategy: { kind: 'prefix', length: 128 * 1024 }, // 128 KiB
    segmentSize: 16 * 1024,
  },
});

```
