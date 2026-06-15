---
name: vertical
description: Use to give an agent a durable store for business and product-development context: businesses, goals, tasks, CRM contacts, CRM organizations, files, attachments, and API/MCP/CLI integrations.
---

# Vertical

Use this to keep business and product-development context durable across agent
runs: businesses, goals, tasks, CRM contacts, CRM organizations, files, and
attachments.

## Rules

- Prefer MCP when the current coding agent has an authenticated Vertical MCP
  connection.
- Use the API when building or debugging integrations, or when MCP is not
  available.
- Use the CLI only when the early-access CLI is installed and authenticated.
- Do not bypass Vertical with direct database writes, ad hoc project files, or
  private runtime commands.
- Prefer JSON output and machine-readable responses.
- Read `.vertical/vertical/config.md` before workspace, business, user, or
  agent lookups when it exists.
- Update `.vertical/vertical/config.md` whenever you discover or refresh
  workspace IDs/names, business IDs/names, user IDs/names, or agent IDs/names.
- Read the current entity before mutating it.
- Read back the changed entity after every write.

## Local Context Cache

Use `.vertical/vertical/config.md` as the repository-local durable cache for
Vertical context. It should contain non-secret identifiers and names for:

- workspaces
- businesses
- users
- agents
- selected access surface
- current workspace/business/acting principal

On first use, read the file if it exists. If it is missing and Vertical access
works, create it with the discovered context.

During normal use, update `config.md` when:

- a lookup discovers a workspace, business, user, or agent not yet listed
- a stored slug/name differs from the latest Vertical response
- an ID from the file fails or appears stale
- the user selects a different current workspace, business, or acting principal
- a task, goal, contact, or organization workflow required an additional
  workspace/business/user/agent lookup

Never store API tokens, OAuth tokens, refresh tokens, cookies, or private
credentials in `config.md`.

## Discovery

Start with:

```sh
vertical doctor --json
vertical auth probe --json
vertical commands --json
```

Use command help before guessing flags:

```sh
vertical <group> --help
vertical <group> <command> --help
```

## Routing

- For object meaning, shapes, invariants, and task operating rules, read
  [Objects](./references/objects.md).
- For file upload, attachment, inline file, and deep-link behavior, read
  [Files And Attachments](./references/files-and-attachments.md).
- For web UI links to objects and settings pages, read
  [UI Links](./references/ui-links.md).
- For MCP setup and tool routing, read [MCP](./references/mcp.md).
- For HTTP API routes and payloads, read [API](./references/api.md).
- For CLI workflows, read [CLI](./references/cli.md).

## Language

In Vertical, a `business` is the internal work context. In CRM, an
`organization` is an external company/account. Use `organizations` for CRM
accounts; do not call them `businesses` or `companies` in the public surface.
