# Vertical CLI Reference

Use the CLI only when the early-access `vertical` binary is installed and
authenticated. Prefer MCP for coding agents when an authenticated MCP connection
is available.

For object shapes and invariants, read [Objects](./objects.md). For file
semantics, read [Files And Attachments](./files-and-attachments.md).

## Setup

Prefer a workspace-local wrapper when a managed workspace provides one:

```sh
./bin/vertical doctor --json
```

Otherwise use the `vertical` command on `PATH`.

```sh
vertical doctor --json
vertical auth probe --json
vertical commands --json
```

For API-token auth:

```sh
export VERTICAL_API_URL="https://api.startwithvertical.com"
export VERTICAL_API_TOKEN="<token>"
```

For CLI OAuth auth:

```sh
export VERTICAL_API_URL="https://api.startwithvertical.com"
export VERTICAL_AUTH_URL="https://auth.startwithvertical.com"
vertical auth login
```

Use a local URL only when the user explicitly says they are working against a
local Vertical stack.

## Operating Rules

- Use `--json` for commands that support it.
- Discover commands with `vertical commands --json`.
- Check command-specific help before guessing flags.
- Read the current record before mutating it.
- Read back the record after every write.
- Leave durable comments or attachments when making important task changes.

## Businesses And Briefs

`business` is the internal Vertical work context. See [Objects](./objects.md)
for business and business brief semantics.

```sh
vertical businesses list --json
vertical businesses get <businessIdOrSlug> --json
vertical businesses create --business-name <name> --business-slug <slug> --json
vertical businesses update <businessIdOrSlug> \
  [--business-name <name>] \
  [--description <markdown> | --description-file <path>] \
  [--production-url <url>] \
  [--staging-url <url>] \
  --json
vertical businesses archive <businessIdOrSlug> [--reason <reason>] --json
vertical businesses reactivate <businessIdOrSlug> --json
vertical businesses rename-slug <businessIdOrSlug> --new-slug <slug> --json
```

Business brief versions:

```sh
vertical businesses brief versions list <businessIdOrSlug> --json
vertical businesses brief versions get <businessIdOrSlug> <versionId> --json
vertical businesses brief versions restore <businessIdOrSlug> <versionId> --json
```

## Goals

Goals are business-scoped strategic outcomes. See [Objects](./objects.md) for
goal shape and invariants.

```sh
vertical goals list --json
vertical goals create --business <businessIdOrSlug> --title <title> --json
vertical goals get <goalId> --json
vertical goals update <goalId> ... --json
vertical goals delete <goalId> --yes --json
vertical goals versions list <goalId> --json
vertical goals versions get <goalId> <versionId> --json
vertical goals versions restore <goalId> <versionId> --json
```

Before creating a new active goal, list existing goals for the business.

## Tasks

Tasks are the durable unit of work. See [Objects](./objects.md) for task shape,
quality rules, duplicate avoidance, blocker semantics, review handoffs, and
completion hygiene.

Prefer `view` when you need comments, events, children, relationships, or
artifacts.

```sh
vertical tasks list [--business <businessIdOrSlug>] [--status <status>] \
  [--priority <priority>] [--label <labelId>] [--limit <number>] --json
vertical tasks search <query> [--business <businessIdOrSlug>] --json
vertical tasks get <taskId> --json
vertical tasks view <taskId> [--with <comments,events,children,relationships,artifacts>] --json
vertical tasks create --business <businessIdOrSlug> --title <title> \
  [--priority <critical|high|medium|low>] [--labels <labelId,labelId>] --json
vertical tasks update <taskId> [--title <title>] [--priority <priority>] ... --json
vertical tasks delete <taskId> --yes --json
vertical tasks assign <taskId> ... --json
vertical tasks complete <taskId> [--comment <body>] [--review-request <instructions>] --json
```

Hierarchy and relationships:

```sh
vertical tasks parent set <taskId> <parentTaskId> --json
vertical tasks parent clear <taskId> --json
vertical tasks children create <taskId> --title <title> --json
vertical tasks relationships list <taskId> [--include-resolved] --json
vertical tasks relationships add <taskId> --type <blocks|related> --to-task <taskId> \
  [--reason <reason>] --json
vertical tasks relationships remove <taskId> <relationshipId> [--reason <reason>] --json
```

For task artifact upload and verification, read
[Files And Attachments](./files-and-attachments.md).

## Contacts

Contacts are business-scoped CRM people. See [Objects](./objects.md) for
contact shape, stage values, and invariants.

Commands:

```sh
vertical contacts list --business <businessIdOrSlug> \
  [--stage <stage>] [--organization <organizationId>] [--search <query>] \
  [--limit <number>] [--sort <sort>] [--cursor <cursor>] --json
vertical contacts create --business <businessIdOrSlug> --name <name> \
  [--email <email>] [--phone <phone>] [--title <title>] \
  [--stage <stage>] [--organization <organizationId>] --json
vertical contacts view <contactId> --json
vertical contacts update <contactId> \
  [--name <name>] [--email <email>] [--phone <phone>] [--title <title>] \
  [--organization <organizationId> | --no-organization] --json
vertical contacts stage <contactId> --stage <stage> --json
vertical contacts delete <contactId> [--reason <reason>] --json
```

Comments:

```sh
vertical contacts comments list <contactId> --json
vertical contacts comments create <contactId> --body <markdown> --json
vertical contacts comments correct <contactId> <commentId> --body <markdown> --json
vertical contacts comments delete <contactId> <commentId> --json
```

## Organizations

Organizations are external CRM accounts/companies. See [Objects](./objects.md)
for organization shape and invariants.

```sh
vertical organizations list --business <businessIdOrSlug> \
  [--search <query>] [--limit <number>] [--sort <sort>] [--cursor <cursor>] --json
vertical organizations create --business <businessIdOrSlug> --name <name> \
  [--domain <domain>] --json
vertical organizations view <organizationId> --json
vertical organizations update <organizationId> \
  [--name <name>] [--domain <domain>] [--website-url <url>] \
  [--linkedin-url <url>] --json
vertical organizations delete <organizationId> [--reason <reason>] --json
```

Comments:

```sh
vertical organizations comments list <organizationId> --json
vertical organizations comments create <organizationId> --body <markdown> --json
vertical organizations comments correct <organizationId> <commentId> --body <markdown> --json
vertical organizations comments delete <organizationId> <commentId> --json
```

## Files And Attachments

Read [Files And Attachments](./files-and-attachments.md) for file upload,
visible attachments, inline files, owner forms, verification, and deep links.
