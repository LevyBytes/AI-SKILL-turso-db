# NFS server ports and mounts

[Context index](filesystem-and-session-workflows.md)

## Starting the NFS Server

AgentFS can serve filesystems through NFS, allowing other machines, containers, or virtual machines to access them remotely.

### Starting the NFS Server

```
agentfs serve nfs my-agent

```

 By default, this listens on `127.0.0.1:11111`.

### Binding to All Interfaces

 To allow remote access:

```
agentfs serve nfs my-agent --bind 0.0.0.0 --port 2049

```

 Binding NFS to `0.0.0.0` exposes the filesystem to any host that can connect to your machine. Use firewall rules or a VPN in production.

### Mounting the Filesystem

### From the Same Machine

```
mkdir /mnt/agentfs
mount -t nfs -o vers=3,tcp,port=11111,mountport=11111,nolock 127.0.0.1:/ /mnt/agentfs

```

### From a Remote Machine

```
mount -t nfs -o vers=3,tcp,port=2049,mountport=2049,nolock server-ip:/ /mnt/agentfs

```

### Mount Options Explained

| Option | Description |
| --- | --- |
| `vers=3` | Use NFSv3 protocol |
| `tcp` | Use TCP transport |
| `port=11111` | NFS server port |
| `mountport=11111` | Mount protocol port |
| `nolock` | Disable file locking (single-user) |

### Unmounting

```
umount /mnt/agentfs

```

 If the mount is busy:

```
umount -f /mnt/agentfs  # Force unmount
# or
umount -l /mnt/agentfs  # Lazy unmount

```

### Next Steps

### MCP Server

Expose AgentFS via MCP

### Sync with Turso Cloud

Persist and share agent state
