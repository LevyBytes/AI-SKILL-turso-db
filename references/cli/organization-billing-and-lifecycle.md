# Organization billing and lifecycle

[Context index](installation-organization-and-account-commands.md) · [Setup and preceding context](api-token-list-and-scopes.md) · [Previous phase](cli-setup-and-command-families.md) · [Continue](organization-members-and-active-selection.md)

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
