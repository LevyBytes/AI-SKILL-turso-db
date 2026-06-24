# Group, Headless Mode, Help, Installation ...

> Cli reference. Original prose; identifiers preserved verbatim.

## Overview

Covers: Abort; Info; Start; Flags; List; Unarchive; Headless Mode; Help; macOS; Introduction; Billing; Create; Destroy; Rm; Switch; Disable; Enable; Select.

## Abort

Abort the AWS migration process with this command:

```
turso group aws-migration abort <group-name>

```

## Info

Use this command to view details for the AWS migration:

```
turso group aws-migration info <group-name>

```

## Start

Start the AWS migration process by running this command:

```
turso group aws-migration start <group-name>

```

## Flags

Create another database group. Every group is attached to one primary region.

```
turso group create <group-name> [flags]

```

 Only Scaler, Pro, and Enterprise plans can create multiple groups. Turso chooses the nearest region automatically as the primary region, unless you set a different one with the `--location` flag.

### Flags

| Flag | Description |
| --- | --- |
| `--canary` | Use database canary build. |
| `--location` | Use a three-digit code to create the group in the requested primary location. |
| `-w`, `--wait` | Do not exit until the group is ready. |

## Flags

Destroy a group together with every database in it by using this command:

```
turso group destroy <group-name> [flags]

```

### Flags

| Flag | Description |
| --- | --- |
| `-y`, `--yes` | Confirms destruction of the group, including every location and database. |

## List

Run this command to retrieve all groups:

```
turso group list

```

## Flags

Create a token for connecting to any database within the group with this command:

```
turso group tokens create <group-name> [flags]

```

### Flags

| Flag | Description |
| --- | --- |
| `-e`, `--expiration` | Token expiration; use `never` or a day-based value such as `7d` . |
| `-r`, `--read-only` | Limit this token to read only access. |

### Examples

 These examples show common patterns for using the `group tokens create` command.

### Create a token with read only access

 Create a read only access token with this command:

```
turso group tokens create <group-name> --read-only

```

### Create a token with a specific expiration time

 Create a token that expires at a specific time by using this command:

```
turso group tokens create <group-name> --expiration 7d

```

## Flags

Run this command to invalidate every token for a group:

```
turso group tokens invalidate <group-name> [flags]

```

### Flags

| Flag | Description |
| --- | --- |
| `-y`, `--yes` | Confirms that all current group tokens should be invalidated. |

## Flags

Transfer a group, including its databases, to an organization where you are an admin or owner with this command:

```
turso group transfer <group-name> <organization-name> [flags]

```

 Current database URL and tokens remain valid, but update your application to the new URL and token as soon as you can.

### Flags

| Flag | Description |
| --- | --- |
| `-y`, `--yes` | Confirms moving the group to a new organization. |

## Unarchive

Unarchive inactive databases by running this command:

```
turso group unarchive <group-name>

```

 For users on a free plan, databases are archived after 10 days without activity.

## Flags

Update the group and all included databases with this command:

```
turso group update <group-name> [flags]

```

### Flags

| Flag | Description |
| --- | --- |
| `--extensions string` | Enable extensions by passing `all` or `none`. |
| `--version string` | Choose the group version to update to. Supported values include `latest` and `canary`. |
| `-y`, `--yes` | Skip confirmation prompt and confirm. |

### Examples

### Update a group to enable all extensions

 Enable `all` extensions by updating a group and all of its databases:

```
turso group update <group-name> --extensions all

```

## Headless Mode

When commands need interaction, the Turso CLI may open a browser or pause for more instructions. Pass the `--headless` flag on operations to avoid that behavior:

```
turso auth login --headless

```

 Use the `--headless` flag when running on Windows with WSL or in a remote CI/CD environment.

## Help

Run this command whenever you need a summary of available commands:

```
turso help

```

 To inspect a command's required and optional arguments, pass the `--help` flag to that command:

```
turso auth --help
turso db create --help

```

 For help integrating Turso with your project, [we can help](https://docs.turso.tech/support).

## macOS

### macOS

 On macOS, the recommended way to install the Turso CLI is Homebrew:

```
brew install tursodatabase/tap/turso

```

 If Homebrew is not your choice, run this command to execute the CLI installer shell script:

```
curl -sSfL https://get.tur.so/install.sh | bash

```

### Linux

 Execute the CLI installer shell script on Linux with this command:

```
curl -sSfL https://get.tur.so/install.sh | bash

```

### Windows

 Windows installation of the Turso CLI requires [WSL](https://learn.microsoft.com/en-us/windows/wsl/install). After WSL is installed, run this in PowerShell:

```
wsl

```

 After that, run the script to install the Turso CLI:

```
curl -sSfL https://get.tur.so/install.sh | bash

```

### Verify installation

 Open a fresh shell, then verify the installation with this command:

```
turso

```

## Introduction

The Turso CLI lets you manage databases, API tokens, user invites, and the database shell from the command line. It also includes a relax command while you wait for a migration. You can manage Turso account resources such as groups, databases, organizations, and invites programmatically through the [Platform API](https://docs.turso.tech/api-reference). 1

Install

First, install the Turso CLI:

```
brew install tursodatabase/tap/turso

```

2

Authenticate

Now signup or login:

```
turso auth signup

```

3

Operate

These commands are available in the Turso CLI:
| Command | Description |
| --- | --- |
| `auth` | Authenticate and manage API tokens. |
| `contact` | Send feedback, share ideas, and schedule time with the team. |
| `db` | Manage databases, create access tokens, and open the shell. |
| `dev` | Run Turso locally for development. |
| `group` | Set up groups for databases that share a location. |
| `org` | Manage billing and invite members. |
| `plan` | `overages`, `select`, `show`, `upgrade` |
| `quickstart` | Start using Turso in 5 minutes. |
| `relax` | Pause for a bit and relax with Turso. |
| `update` | Upgrade the Turso CLI to the latest version with a single command. |

## Billing

Open the billing portal for your organization with this command:

```
turso org billing

```

## Create

Run this command to create a new organization that you own:

```
turso org create <name>

```

 To finish creating a new organization, you must add a payment method and subscribe to the Scaler plan.

## Destroy

Delete an organization you own by running this command:

```
turso org destroy <organization-slug>

```

 Before deleting an organization, transfer or remove any existing projects. A personal account or active organization cannot also be destroyed by users.

## List

List organizations where you are an owner or member with this command:

```
turso org list

```

## Flags

Add an existing Turso user to the current active organization by running this command:

```
turso org members add <username> [flags]

```

### Flags

| Flag | Description |
| --- | --- |
| `-a`, `--admin` | Grant the user admin status. |

## Flags

Invite a person who is not yet a Turso user to the current active organization with this command:

```
turso org members invite <email> [flags]

```

### Flags

| Flag | Description |
| --- | --- |
| `-a`, `--admin` | Grant the user admin status. |

## List

Run this command to list every member of the current active organization:

```
turso org members list

```

 This command works only with active organizations; use [`org switch`](https://docs.turso.tech/cli/org/switch) if you are authenticated under your personal account.

## Rm

Remove a member from the current organization with this command:

```
turso org members rm <username>

```

## Switch

Use this command to change the CLI's current active organization:

```
turso org switch <organization-slug>

```

## Disable

Disable overages for the current repository by running this command:

```
turso plan overages disable

```

 Any overages already incurred will still appear as charges.

## Enable

Enable overages for the current repository by running this command:

```
turso plan overages enable

```

## Select

Change your current plan whenever needed with this command:

```
turso plan select

```

 Before upgrading to a paid plan, add credit card details to your account. [Learn more](https://turso.tech/pricing) about pricing.

## Show

Display current plan usage and limits with this command:

```
turso plan show

```

## Upgrade

Upgrade your account to a paid plan at any time with this command:

```
turso plan upgrade

```

 A paid-plan upgrade requires credit card details on your account.

## macOS

### macOS

```
brew update
brew upgrade

```

### Linux

```
turso update

```

### Windows

```
turso update

```
