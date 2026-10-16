# Edge replica locations and availability

[Context index](vectors-attach-branches-and-edge-replicas.md)

## How it works

This feature is deprecated for all new users; existing users may continue using Edge Replicas on Fly - [read the announcement](https://turso.tech/blog/upcoming-changes-to-the-turso-platform-and-roadmap). For maximum speed, Turso can [embed databases](https://docs.turso.tech/features/embedded-replicas) directly into the application on the same node. That setup removes inter-regional request hops, effectively reducing latency to zero.

### How it works

1. Create a database in a primary location
2. Add extra locations where the data should be replicated
3. Query a single URL, which routes automatically to the closest edge

### Add replica location

 Add locations to a database group with the [Turso CLI](https://docs.turso.tech/cli/group/locations/add) or [Platform API](https://docs.turso.tech/api-reference/groups/add-location):

```
turso group locations add <group-name> <location-code>

```

### Remove replica location

 Remove locations from a database group with the [Turso CLI](https://docs.turso.tech/cli/group/locations/remove) or [Platform API](https://docs.turso.tech/api-reference/groups/remove-location):

```
turso group locations remove <group-name> <...location-codes>

```
