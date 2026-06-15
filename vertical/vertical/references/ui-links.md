# Vertical UI Links Reference

Use this reference when linking a user to Vertical web UI pages.

## Basics

Production app origin:

```text
https://startwithvertical.com
```

Most app URLs are scoped by business slug:

```text
https://startwithvertical.com/<businessSlug>/...
```

Use the `businessSlug` from the Vertical object response or from
`.vertical/vertical/config.md`. Do not build UI links from IDs alone when a
business slug is required.

Prefer a Vertical-generated deep link when one exists. Otherwise build the
direct route below.

## Main Business Pages

```text
/<businessSlug>
/<businessSlug>/dashboard
/<businessSlug>/workspace
/<businessSlug>/brief
/<businessSlug>/tasks
/<businessSlug>/goals
/<businessSlug>/contacts
/<businessSlug>/organizations
/<businessSlug>/chats
```

Examples:

```text
https://startwithvertical.com/acme/tasks
https://startwithvertical.com/acme/brief
```

## Object Detail Pages

Tasks:

```text
/<businessSlug>/tasks/<taskId>
/<businessSlug>/tasks/<taskId>/chat
```

Goals:

```text
/<businessSlug>/goals/<goalId>
/<businessSlug>/goals/<goalId>/overview
/<businessSlug>/goals/<goalId>/activity
/<businessSlug>/goals/<goalId>/chat
```

Contacts:

```text
/<businessSlug>/contacts/<contactId>
```

Organizations:

```text
/<businessSlug>/organizations/<organizationId>
```

Chats:

```text
/<businessSlug>/chats/<conversationId>
```

## Settings Pages

Settings root:

```text
/<businessSlug>/settings
```

Account settings:

```text
/<businessSlug>/settings/account
/<businessSlug>/settings/account/profile
/<businessSlug>/settings/account/security
```

Business settings:

```text
/<businessSlug>/settings/business
/<businessSlug>/settings/business/general
```

Workspace settings:

```text
/<businessSlug>/settings/workspace
/<businessSlug>/settings/workspace/general
/<businessSlug>/settings/workspace/members
/<businessSlug>/settings/workspace/agents
/<businessSlug>/settings/workspace/api
```

Workspace selector:

```text
/workspaces/select
```

## Deep-Link Surfaces

The public deep-link resolver is available through the API and early-access CLI.
The current MCP tool list does not expose a generic `vertical_deep_links_*`
tool. MCP file and placement tools may still return `deepLinkUrl` or
`openDeepLinkUrl` fields in their results; use those when present.

The resolver supports these target types:

```text
task
comment
goal
placement
```

### API

```http
POST /api/v1/deep-links/resolve
```

Body:

```json
{
  "target": { "type": "task", "id": "tsk_..." },
  "open": false
}
```

### CLI

```sh
vertical deep-links task <taskId> --json
vertical deep-links comment <commentId> --json
vertical deep-links goal <goalId> --json
vertical deep-links placement <att_...|ref_...> [--open] --json
```

Use the returned `deepLinkUrl`. If `openDeepLinkUrl` is present and the user
asked to open a previewable file directly, use `openDeepLinkUrl`.

### MCP

There is no generic MCP deep-link resolver tool in the current tool surface. For
MCP workflows:

- Use `deepLinkUrl` or `openDeepLinkUrl` fields returned by MCP file,
  attachment, or placement tools when available.
- For tasks, goals, contacts, organizations, chats, and settings pages, build
  the direct UI URL from the object response's `businessSlug` and ID.
- If an exact task/comment/goal/placement resolver link is required and MCP does
  not return one, use the API resolver or CLI resolver.

## Comment Links

Task comment deep links resolve to the parent task page with a `comment` query
parameter:

```text
/<businessSlug>/tasks/<taskId>?comment=<commentId>
```

The public deep-link resolver currently resolves task comments. For contact or
organization comments, link to the parent contact or organization detail page
unless the API returns a more specific deep link.

## File Placement Links

File placement links target the owning UI page with query parameters.

Visible attachment:

```text
/<businessSlug>/tasks/<taskId>?attachment=<attachmentId>
/<businessSlug>/contacts/<contactId>?attachment=<attachmentId>
/<businessSlug>/organizations/<organizationId>?attachment=<attachmentId>
/<businessSlug>/goals/<goalId>?attachment=<attachmentId>
```

Inline file:

```text
/<businessSlug>/tasks/<taskId>?ref=<referenceId>
/<businessSlug>/tasks/<taskId>?comment=<commentId>&ref=<referenceId>
/<businessSlug>/goals/<goalId>?ref=<referenceId>
```

Previewable placement open link:

```text
/<businessSlug>/tasks/<taskId>?attachment=<attachmentId>&open=1
/<businessSlug>/tasks/<taskId>?comment=<commentId>&ref=<referenceId>&open=1
```

Prefer resolving placement links through `vertical deep-links placement ...`
or `POST /api/v1/deep-links/resolve` because the resolver knows the owning
object and whether `open=1` is supported.

## Desktop Links

The desktop app accepts encoded Vertical web targets:

```text
vertical://open?url=<encoded Vertical web URL>
```

Example target before encoding:

```text
https://startwithvertical.com/acme/tasks
```

Use normal web URLs in docs, comments, and user-facing text unless the user
specifically asks for a desktop deep link.

## Rules

- Use web UI links for humans; use API URLs only for integrations.
- Use `https://startwithvertical.com` for production examples.
- Include the business slug in business-scoped routes.
- Prefer resolver-generated `deepLinkUrl` for tasks, task comments, goals, and
  file placements.
- For contacts and organizations, build direct detail links from the object
  response's `businessSlug` and ID.
- Do not include API tokens, OAuth tokens, or session data in links.
