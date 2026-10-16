# TypeScript file reading and metadata

[Context index](agentfs-language-sdks.md) · [Setup and preceding context](typescript-setup-and-agent-options.md) · [Previous phase](typescript-key-value-and-file-writing.md) · [Continue](typescript-tool-records-and-browser-usage.md)

#### `fs.readFile(path: string, options?: BufferEncoding): Promise<Buffer | string>`

 Read the contents of a file. By default this returns a Buffer; specify an encoding to receive a string.

```
// Read as Buffer (default)
const data = await agent.fs.readFile('/reports/summary.md');
const text = data.toString('utf-8');

// Read as string with encoding
const content = await agent.fs.readFile('/reports/summary.md', 'utf-8');

// For JSON files
const jsonData = await agent.fs.readFile('/config.json');
const config = JSON.parse(jsonData.toString());

```

#### `fs.readdir(path: string): Promise<string[]>`

 List directory contents.

```
const files = await agent.fs.readdir('/reports');
// Returns: ['summary.md', '2024/', 'archive/']

// Check each entry type
for (const entry of files) {
  const stats = await agent.fs.stat(`/reports/${entry}`);
  if (stats.isDirectory()) {
    console.log(`Directory: ${entry}`);
  } else {
    console.log(`File: ${entry} (${stats.size} bytes)`);
  }
}

```

#### `fs.stat(path: string): Promise<Stats>`

 Get file or directory metadata.

```
interface Stats {
  size: number;        // File size in bytes
  mode: number;        // File mode/permissions
  mtime: number;       // Modified time (Unix timestamp)
  ctime: number;       // Created time (Unix timestamp)
  isFile(): boolean;
  isDirectory(): boolean;
}

const stats = await agent.fs.stat('/reports/summary.md');
console.log(`Size: ${stats.size} bytes`);
console.log(`Modified: ${new Date(stats.mtime * 1000)}`);

```

#### `fs.exists(path: string): Promise<boolean>`

 Determine whether a file or directory is present.

```
if (await agent.fs.exists('/reports/draft.md')) {
  console.log('Draft exists');
}

```

#### `fs.deleteFile(path: string)`

 Delete a file.

```
await agent.fs.deleteFile('/reports/old.md');

```

### Tool Call Tracking API

 Store and query agent tool invocations for compliance and debugging workflows.
