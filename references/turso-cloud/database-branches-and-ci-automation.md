# Database branches and CI automation

[Context index](vectors-attach-branches-and-edge-replicas.md)

## How it works

A branch is an independent database instance created from an existing database. You may also create one from a database [point-in-time](https://docs.turso.tech/features/point-in-time-recovery) snapshot. Branches help development and testing because changes can be made without impacting the original database.

### How it works

1. Create a new database from an existing database through the CLI or API.
2. Connect to that new database with the group API token.
3. Optionally use a migration tool to change the new schema.
4. When merging through a GitHub Action, optionally use a migration tool to apply the changes to the original database.
5. Delete the database after it is no longer needed.

### Usage

 Create a new database from an existing database with either the CLI or API:

```
turso db create my-new-database-branch --from-db my-existing-database

```

 For details on every argument, see these references:

- [CLI database creation](https://docs.turso.tech/cli/db/create)
- [Platform API database creation](https://docs.turso.tech/api-reference/databases/create)

### Things to know

- Database branches are fully separate from the original database, so any schema changes or data must be merged manually with a migration tool.
- To connect to the new database, [create a new token](https://docs.turso.tech/cli/db/tokens/create) or use a group token.
- Delete the database branch manually after it is no longer needed.
- Branches count against your plan’s database quota.

### CI/CD

 Branch automation is useful when each pull request needs a new database, because changes can be tested without affecting the original database. This example shows one possible setup with the [Platform API](https://docs.turso.tech/api-reference/databases/create): .github/workflows/create-database-branch.yml

```yaml
name: Create Database
on: create
permissions: {}

jobs:
  triggerAPI:
    if: github.ref_type == 'branch'
    runs-on: ubuntu-latest
    steps:
      - name: Generate Branch Name
        id: branch_name
        env:
          BRANCH_REF: ${{ github.ref_name }}
        run: |
          node --input-type=module <<'JS'
          import { appendFileSync } from 'node:fs';
          const stem = process.env.BRANCH_REF.toLowerCase().replace(/[^a-z0-9]/g, '').slice(0, 29);
          if (!stem) throw new Error('Branch name has no usable characters');
          appendFileSync(process.env.GITHUB_OUTPUT, `branch_name=db-${stem}\n`);
          JS
      - name: Create Database
        env:
          BRANCH_NAME: ${{ steps.branch_name.outputs.branch_name }}
          API_TOKEN: ${{ secrets.API_TOKEN }}
          DATABASE_NAME: ${{ secrets.DATABASE_NAME }}
          ORGANIZATION_NAME: ${{ secrets.ORGANIZATION_NAME }}
        run: |
          node --input-type=module <<'JS'
          import { writeFileSync } from 'node:fs';
          const { BRANCH_NAME, DATABASE_NAME, ORGANIZATION_NAME } = process.env;
          if (!/^[a-z0-9-]+$/.test(ORGANIZATION_NAME ?? '')) throw new Error('Invalid organization slug');
          if (!DATABASE_NAME || !process.env.API_TOKEN) throw new Error('Missing repository secrets');
          writeFileSync('payload.json', JSON.stringify({
            name: BRANCH_NAME, group: 'default',
            seed: { type: 'database', name: DATABASE_NAME }
          }));
          JS
          curl --fail-with-body -X POST \
            -H "Authorization: Bearer $API_TOKEN" \
            -H "Content-Type: application/json" \
            --data-binary @payload.json \
            "https://api.turso.tech/v1/organizations/$ORGANIZATION_NAME/databases"

```
