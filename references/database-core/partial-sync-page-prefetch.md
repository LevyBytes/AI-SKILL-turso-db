# Partial sync page prefetch

[Context index](database-sync-workflows.md) · [Setup and preceding context](partial-sync-bootstrap-and-segment-size.md)

### Prefetch

Read page 4 → prefetch 2 child pages (11, 12)

 Prefetch is an optional improvement layered on lazy page fetching. When active, the client retrieves the pages needed for the current query, then reviews the newly fetched page structure plus recent access history to estimate which pages may be useful next. If the access pattern appears to continue naturally, for example through child pages referenced by an internal B-tree node, the client downloads those pages early. This can lower the number of later on-demand fetches and reduce pauses during range scans, index walks, or sequential lookups.

```javascript
import { connect } from '@tursodatabase/sync';

await connect({
  path: './app.db',
  url: process.env.TURSO_DATABASE_URL,
  authToken: process.env.TURSO_AUTH_TOKEN,
  partialSyncExperimental: {
    bootstrapStrategy: { kind: 'prefix', length: 128 * 1024 }, // 128 KiB
    prefetch: true,
  },
});

```

 `segment_size` and `prefetch` work well together. Segment size groups nearby pages into one on-demand fetch, while prefetch examines the query's access pattern and downloads additional likely-needed pages ahead of time. Combining them often gives the strongest performance on realistic workloads.
