# TypeScript key value and file writing

[Context index](agentfs-language-sdks.md) · [Setup and preceding context](typescript-setup-and-agent-options.md) · [Continue](typescript-file-reading-and-metadata.md)

### Key-Value Store API

 Fast typed storage for agent configuration and state.

#### `kv.set(key: string, value: any)`

 Save a value and JSON-serialize it automatically.

```
await agent.kv.set('user:123', {
  name: 'Alice',
  preferences: { theme: 'dark' }
});

await agent.kv.set('session:current', 'abc-123');
await agent.kv.set('counter', 42);

```

#### `kv.get<T>(key: string): Promise<T | undefined>`

 Load a value and automatically deserialize it.

```
interface UserData {
  name: string;
  preferences: { theme: string };
}

const user = await agent.kv.get<UserData>('user:123');
if (user) {
  console.log(user.name); // Type-safe access
}

```

#### `kv.delete(key: string)`

 Remove a key-value pair.

```
await agent.kv.delete('session:expired');

```

#### `kv.list(prefix: string): Promise<{ key: string, value: any }[]>`

 List keys matching a prefix.

```
// List all user keys
const userEntries = await agent.kv.list('user:');
for (const { key, value } of userEntries) {
  console.log(key, value);
}

// List all keys (empty prefix)
const allEntries = await agent.kv.list('');

```

### Filesystem API

 POSIX-style filesystem operations used to manage agent data.

#### `fs.writeFile(path: string, data: string | Buffer)`

 Write data into a file and create missing parent directories when necessary.

```
// Write text
await agent.fs.writeFile('/reports/summary.md', '# Report\nContent...');

// Write binary data
const imageBuffer = await fetch(url).then(r => r.arrayBuffer());
await agent.fs.writeFile('/images/chart.png', Buffer.from(imageBuffer));

```
