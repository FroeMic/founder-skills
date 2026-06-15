# Vertical MCP Reference

Use MCP when the coding agent has a configured Vertical remote HTTP MCP server.

For object shapes and invariants, read [Objects](./objects.md). For file
semantics, read [Files And Attachments](./files-and-attachments.md).

## Model

Vertical MCP is a remote HTTP MCP surface. The production MCP endpoint is:

```text
https://mcp.startwithvertical.com/mcp
```

It is OAuth-protected and scope-gated. The MCP server validates bearer access
tokens whose audience is the MCP resource.

The production OAuth authorization server is:

```text
https://auth.startwithvertical.com
```

Do not copy normal API tokens into MCP config unless Vertical explicitly
documents that mode. Prefer the OAuth flow offered by the MCP client.

## Setup

1. Create a Vertical account:

```text
https://startwithvertical.com/create
```

2. Add the production Vertical MCP URL to the coding agent's MCP configuration,
   unless the user explicitly wants local or staging:

```text
https://mcp.startwithvertical.com/mcp
```

3. Complete the OAuth flow in the MCP client.
4. Probe the connection with `vertical_auth_probe` if the client exposes tools
   directly.

Local development commonly uses an MCP service on port `3217`; staging and
local stack URLs should be used only when the user asks for that environment.

## Scopes

Default practical scopes:

```text
vertical:read vertical:write vertical:files offline_access
```

Scope meanings:

- `vertical:read`: read workspace context and product data.
- `vertical:write`: create and update normal workspace records.
- `vertical:files`: upload/download/read file bytes and manage attachments.
- `vertical:admin`: explicit elevated workspace/admin grant.
- `offline_access`: OAuth refresh-token request, not a Vertical permission.

Request `vertical:admin` only for workspace/security/admin workflows.

## Client Compatibility

Claude Code can use the known OAuth client flow.

Cursor, Codex URL-only setup, and generic remote MCP clients should use OAuth
Dynamic Client Registration when the Vertical auth service advertises a
`registration_endpoint`.

The expected discovery flow is:

```text
GET https://mcp.startwithvertical.com/.well-known/oauth-protected-resource/mcp
GET <authorization server from resource metadata>/.well-known/oauth-authorization-server
POST /oauth/register
authorization_code + PKCE OAuth flow
```

## Tool Groups

Tool visibility depends on the authenticated principal and scopes.

Read/write tools include:

- `vertical_auth_probe`
- `vertical_businesses_*`
- `vertical_users_*`
- `vertical_agents_*`
- `vertical_labels_*`
- `vertical_tasks_*`
- `vertical_comments_*`
- `vertical_contacts_*`
- `vertical_contact_comments_*`
- `vertical_organizations_*`
- `vertical_organization_comments_*`
- `vertical_goals_*`
- `vertical_files_*`
- `vertical_attachments_*`
- `vertical_events_list`

Admin tools require `vertical:admin` and an admin-capable principal. Examples:

- `vertical_businesses_create`
- `vertical_businesses_update`
- `vertical_businesses_archive`
- `vertical_businesses_reactivate`
- `vertical_invitations_*`
- `vertical_members_*`

## Operating Rules

- Use MCP tools directly when the client exposes them.
- If a tool fails because of scope, do not retry with broader assumptions; ask
  the user for the needed grant.
- Read before mutating and read back after writes.
- Prefer object and access-layer references for exact domain semantics when an
  MCP tool name is available but its schema is not visible in the current
  client.
