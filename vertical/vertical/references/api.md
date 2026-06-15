# Vertical API Reference

Use the API when building integrations, debugging exact HTTP behavior, or when
the CLI/MCP surface is unavailable.

For object shapes and invariants, read [Objects](./objects.md). For file
semantics, read [Files And Attachments](./files-and-attachments.md).

## Basics

Production base URL:

```text
https://api.startwithvertical.com
```

The API prefix is:

```text
/api/v1
```

Send bearer auth on every `/api/v1` request:

```http
Authorization: Bearer <token>
```

Success envelope:

```json
{ "ok": true, "data": {} }
```

List envelope:

```json
{ "ok": true, "data": [], "page": { "next_cursor": null } }
```

Error envelope:

```json
{ "ok": false, "error": { "code": "not_found", "message": "..." } }
```

## Auth And Scopes

Probe auth:

```http
GET /api/v1/auth/probe
```

Scopes:

- `vertical:read`: read workspace data and metadata.
- `vertical:write`: create and update normal workspace records.
- `vertical:files`: upload, download, and attach files.
- `vertical:admin`: explicit workspace/security/admin authority.

Use the smallest practical credential. Normal automation usually needs
`vertical:read vertical:write vertical:files`. Keep admin credentials separate.

## Businesses

`business` is the internal Vertical work context. See [Objects](./objects.md)
for business and business brief shapes.

```http
GET /api/v1/businesses
POST /api/v1/businesses
GET /api/v1/businesses/:businessIdOrSlug
PATCH /api/v1/businesses/:businessIdOrSlug
POST /api/v1/businesses/:businessIdOrSlug/archive
POST /api/v1/businesses/:businessIdOrSlug/reactivate
POST /api/v1/businesses/:businessIdOrSlug/rename-slug
GET /api/v1/businesses/:businessIdOrSlug/brief/versions
GET /api/v1/businesses/:businessIdOrSlug/brief/versions/:versionId
POST /api/v1/businesses/:businessIdOrSlug/brief/versions/:versionId/restore
```

For direct business file resources and inline business-brief files, read
[Files And Attachments](./files-and-attachments.md).

## Goals

Goals are business-scoped strategic outcomes. See [Objects](./objects.md) for
goal shape and invariants.

```http
GET /api/v1/goals
POST /api/v1/goals
GET /api/v1/goals/:goalId
PATCH /api/v1/goals/:goalId
DELETE /api/v1/goals/:goalId
GET /api/v1/goals/:goalId/versions
GET /api/v1/goals/:goalId/versions/:versionId
POST /api/v1/goals/:goalId/versions/:versionId/restore
```

Goal list/detail reads exclude deleted goals. Delete is a logical product
delete, not a public purge operation.

## Tasks

Tasks are the durable unit of work. See [Objects](./objects.md) for task shape,
status semantics, relationship semantics, and task operating rules.

```http
GET /api/v1/tasks
POST /api/v1/tasks
GET /api/v1/tasks/:taskId
GET /api/v1/tasks/:taskId/context
PATCH /api/v1/tasks/:taskId
DELETE /api/v1/tasks/:taskId
PATCH /api/v1/tasks/:taskId/parent
POST /api/v1/tasks/:taskId/assign
POST /api/v1/tasks/:taskId/complete
GET /api/v1/tasks/:taskId/relationships
POST /api/v1/tasks/:taskId/relationships
DELETE /api/v1/tasks/:taskId/relationships/:relationshipId
POST /api/v1/tasks/:taskId/children
```

Task list/detail reads exclude deleted tasks. Relationship delete removes the
relationship from active reads and blocker calculations.

## Task Comments

```http
GET /api/v1/tasks/:taskId/comments
GET /api/v1/tasks/:taskId/comments/:commentId
POST /api/v1/tasks/:taskId/comments
POST /api/v1/tasks/:taskId/comments/:commentId/correct
POST /api/v1/tasks/:taskId/comments/:commentId/delete
```

For direct comment file resources and inline comment-body files, read
[Files And Attachments](./files-and-attachments.md).

## Contacts

Contacts are business-scoped CRM people. See [Objects](./objects.md) for
contact shape, stage values, and invariants.

```http
GET /api/v1/businesses/:businessIdOrSlug/contacts
POST /api/v1/businesses/:businessIdOrSlug/contacts
GET /api/v1/contacts/:contactId
PATCH /api/v1/contacts/:contactId
POST /api/v1/contacts/:contactId/stage
POST /api/v1/contacts/:contactId/delete
GET /api/v1/contacts/:contactId/comments
GET /api/v1/contacts/:contactId/comments/:commentId
POST /api/v1/contacts/:contactId/comments
POST /api/v1/contacts/:contactId/comments/:commentId/correct
POST /api/v1/contacts/:contactId/comments/:commentId/delete
```

List filters include `stage`, `organizationId`, `search`, `limit`, `sort`, and
`cursor`.

For contact attachments and inline files in contact comments, read
[Files And Attachments](./files-and-attachments.md).

## Organizations

Organizations are external CRM accounts/companies. See [Objects](./objects.md)
for organization shape and invariants.

```http
GET /api/v1/businesses/:businessIdOrSlug/organizations
POST /api/v1/businesses/:businessIdOrSlug/organizations
GET /api/v1/organizations/:organizationId
PATCH /api/v1/organizations/:organizationId
POST /api/v1/organizations/:organizationId/delete
GET /api/v1/organizations/:organizationId/comments
GET /api/v1/organizations/:organizationId/comments/:commentId
POST /api/v1/organizations/:organizationId/comments
POST /api/v1/organizations/:organizationId/comments/:commentId/correct
POST /api/v1/organizations/:organizationId/comments/:commentId/delete
```

List filters include `search`, `limit`, `sort`, and `cursor`.

An organization can have many contacts. A contact can optionally belong to one
organization. Assign or clear that relationship through the contact update
surface.

For organization attachments and inline files, read
[Files And Attachments](./files-and-attachments.md).

## Files And Attachments

Files store bytes. Attachments and inline references place files on product
entities. Read [Files And Attachments](./files-and-attachments.md) for the
mental model, owners, verification rules, and deep links.

```http
POST /api/v1/files
GET /api/v1/files/:fileId
GET /api/v1/files/:fileId/content
GET /api/v1/files/:fileId/placements
DELETE /api/v1/files/:fileId
GET /api/v1/attachments?ownerType=<type>&ownerId=<id>
POST /api/v1/attachments
POST /api/v1/attachments/upload
DELETE /api/v1/attachments/:attachmentId
POST /api/v1/files/inline-upload
GET /api/v1/files/inline?ownerType=<type>&ownerId=<id>
```

Browser file routes use browser session cookies and are separate from the bearer
token API surface.

## Related Production Origins

Use API URLs only for integrations. Human-facing links belong on the web app,
and browser-rendered file links belong on the uploads host:

```text
Web app: https://startwithvertical.com
API: https://api.startwithvertical.com
Auth: https://auth.startwithvertical.com
MCP: https://mcp.startwithvertical.com/mcp
Uploads: https://uploads.startwithvertical.com
Public assets: https://public.startwithvertical.com
```
