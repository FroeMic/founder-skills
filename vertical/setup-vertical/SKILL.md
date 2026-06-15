---
name: setup-vertical
description: Use when setting up Vertical access for a repository or coding agent. Creates local `.vertical/vertical/` configuration notes, keeps project-specific state out of the skills source repo, and helps configure MCP, API, or early-access CLI access.
---

# Setup Vertical

Set up the target repository for Vertical access.

## Workflow

1. Confirm you are in the repository where Vertical will be used.
2. Ensure `.vertical/` is ignored in `.gitignore`.
3. Create this structure if missing:

```text
.vertical/
  vertical/
    config.md
```

4. Do not overwrite existing files. If `config.md` exists, read it and preserve
   its content.
5. Ask which Vertical access surface the user wants to use:
   - MCP: recommended default for coding agents that support remote MCP and
     OAuth.
   - API: best when building or debugging integrations.
   - CLI: early access. Use only if the user has access to the private Vertical
     GitHub repo or already has the CLI installed.
   - Auto-detect: prefer MCP if connected, otherwise API, otherwise CLI when
     available.
6. Tell the user they need a Vertical account first:

```text
https://startwithvertical.com/create
```

7. Probe the authenticated Vertical account when possible and capture the
   selected access surface, workspace, businesses, users, and agents in
   `.vertical/vertical/config.md`.
8. During setup, update `config.md` with any IDs discovered through MCP, API, or
   CLI lookups. Do not store secrets or bearer tokens in this file.

## Local Config

`.vertical/vertical/config.md` is the local durable context cache for this
repository. It should contain stable IDs and names that help agents avoid
re-discovering the same workspace context.

Seed missing files with this structure:

```markdown
# Vertical Config

Last updated:

## Access

Preferred surface: MCP
API URL: https://startwithvertical.com
MCP URL:
CLI available: unknown

## Current Context

Workspace:
- id:
- slug:
- name:

Business:
- id:
- slug:
- name:

Acting principal:
- kind: user
- id:
- name:

## Workspaces

| ID | Slug | Name | Notes |
|----|------|------|-------|

## Businesses

| ID | Slug | Name | Workspace | Notes |
|----|------|------|-----------|-------|

## Users

| ID | Name | Email | Workspace | Notes |
|----|------|-------|-----------|-------|

## Agents

| ID | Name | Workspace | Business/Role | Notes |
|----|------|-----------|---------------|-------|

## Notes
```

Rules:

- Keep `.vertical/vertical/config.md` in the target repository only.
- Ensure `.vertical/` is ignored by git.
- Store IDs, names, slugs, selected surface, and non-secret URLs.
- Never store API tokens, OAuth access tokens, refresh tokens, cookies, or
  private credentials.
- If setup discovers more than one workspace or business, record all of them and
  mark the selected current context.
- If a later lookup shows stale IDs, renamed records, or missing context, update
  this file immediately.

## Auth Options

### MCP With OAuth

Use this as the default setup when the coding agent supports remote HTTP MCP.

- Connect the Vertical MCP URL from the user's Vertical account.
- Let the client complete the OAuth flow.
- Default practical scopes are `vertical:read vertical:write vertical:files`.
- Request `vertical:admin` only for explicit workspace/security/admin work.

MCP access uses OAuth access tokens bound to the MCP resource. Do not copy API
tokens into MCP client config unless Vertical explicitly documents that mode.

## Multiple Workspaces Or Accounts

Vertical credentials are workspace-scoped.

### MCP

One MCP OAuth grant is bound to one workspace. Do not assume the agent can
switch workspaces inside one MCP session.

For multiple workspaces, configure one MCP server entry per workspace, each with
its own OAuth grant. Use clear names:

```text
vertical-core
vertical-acme
vertical-labs
```

Record each connected workspace in `.vertical/vertical/config.md`, and mark the
current workspace for this repository.

### API And CLI

API tokens and CLI OAuth credentials are also workspace-scoped. There is no
general `vertical workspaces use` assumption in these skills.

For multiple workspaces, use separate credentials or config profiles, such as:

- different workspace API tokens
- different OAuth login/config files
- separate `VERTICAL_CONFIG_PATH` values when the CLI supports them
- explicit `VERTICAL_API_URL` and `VERTICAL_API_TOKEN` environment variables for
  the workspace being used

Before writing data, verify the active credential with an auth probe and compare
the returned workspace to `.vertical/vertical/config.md`.

### Multiple Businesses

Multiple businesses inside one workspace are normal. Use `--business
<businessIdOrSlug>` or the equivalent API/MCP field to choose the business for
business-scoped work.

### Direct API

Use this when implementing integrations, debugging exact HTTP behavior, or when
MCP is not available.

```sh
export VERTICAL_API_URL="https://startwithvertical.com"
export VERTICAL_API_TOKEN="<token>"
curl -H "Authorization: Bearer $VERTICAL_API_TOKEN" \
  "$VERTICAL_API_URL/api/v1/auth/probe"
```

### CLI With API Token

The CLI is early access. Use it only when the user has access to the private
Vertical GitHub repo or already has a working `vertical` binary installed.

Use this when the user has a workspace API token or workspace-agent API token.

```sh
export VERTICAL_API_URL="https://startwithvertical.com"
export VERTICAL_API_TOKEN="<token>"
vertical auth probe --json
vertical doctor --json
```

### CLI With OAuth

The CLI is early access. Use this only when the installed `vertical` CLI
supports OAuth login.

```sh
vertical auth login
vertical auth status --json
vertical auth refresh --json
vertical auth probe --json
```

## Coding Agents

Cursor, Claude Code, and Codex generally know how to install and load skills.
For Vertical, the important setup is the access surface and auth:

- Prefer MCP when the agent supports remote MCP.
- If using MCP, add the Vertical MCP URL to the agent's MCP configuration and
  complete OAuth.
- If using API, set `VERTICAL_API_URL` and `VERTICAL_API_TOKEN`.
- If using CLI, first confirm early-access CLI availability, then make sure
  `vertical` is on `PATH` and auth works.

After setup, use the `vertical` skill for day-to-day Vertical operations.
