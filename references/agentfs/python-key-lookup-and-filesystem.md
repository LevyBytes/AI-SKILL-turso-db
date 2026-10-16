# Python key lookup and filesystem

[Context index](agentfs-language-sdks.md) · [Setup and preceding context](python-setup-and-key-value-storage.md) · [Continue](python-tool-call-tracking-and-statistics.md)

### Get a Value

```
user = await agent.kv.get('user:123')
# Returns None if key doesn't exist

```

### List Keys

```
# List all keys with prefix
keys = await agent.kv.list('user:')
# Returns: ['user:123', 'user:456', ...]

```

### Delete a Key

```
await agent.kv.delete('user:123')

```

### Filesystem

 POSIX-like filesystem operations.

### Write a File

 Creates parent directories automatically:

```
await agent.fs.write_file('/data/config.json', '{"key": "value"}')

# Write bytes
await agent.fs.write_file('/data/image.png', image_bytes)

```

### Read a File

```
# Read as string (default)
content = await agent.fs.read_file('/data/config.json')

# Read as bytes
data = await agent.fs.read_file('/data/image.png', encoding=None)

```

### List Directory

```
entries = await agent.fs.readdir('/data')
# Returns: ['config.json', 'image.png', ...]

```

### Get File Stats

```
stats = await agent.fs.stat('/data/config.json')
print(f"Size: {stats.size} bytes")
print(f"Modified: {stats.mtime}")
print(f"Is file: {stats.is_file()}")
print(f"Is directory: {stats.is_dir()}")

```

### Delete a File

```
await agent.fs.delete_file('/data/config.json')

```

### Create Directory

```
await agent.fs.mkdir('/data/subdir')

```
