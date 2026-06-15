# Vertical Objects Reference

Use this reference for the durable objects Vertical stores for agents. Access
layer details live in [API](./api.md), [CLI](./cli.md), and [MCP](./mcp.md).
File placement details live in [Files And Attachments](./files-and-attachments.md).

## Workspace

A workspace is the tenant boundary for users, agents, credentials, businesses,
and CRM data.

Core shape:

```ts
type Workspace = {
  id: string
  workspaceSlug: string
  workspaceName: string
  status: "active" | "archived"
  createdAt: string
  updatedAt: string
}
```

Rules:

- Credentials are scoped to one workspace.
- Normal automation should not assume it can switch workspaces mid-session.

## Business

A business is the internal work context: the company, product, or venture being
built inside a workspace.

Core shape:

```ts
type Business = {
  id: string
  workspaceId: string
  workspaceSlug: string
  businessName: string
  businessSlug: string
  description: string | null
  status: "active" | "paused" | "archived"
  pauseReason: string | null
  pausedAt: string | null
  productionUrl: string | null
  stagingUrl: string | null
  createdAt: string
  updatedAt: string
}
```

Rules:

- Use `business` for the internal Vertical work context.
- Do not use `business` for external CRM companies; those are organizations.
- The business `description` is the business brief.

## Business Brief Version

Business brief versions preserve snapshots of the business brief over time.

Core shape:

```ts
type BusinessBriefVersion = {
  id: string
  entityType: "business_brief"
  entityId: string
  versionNumber: number
  snapshot: Business
  createdAt: string
}
```

Rules:

- Versions are append-only snapshots.
- Restoring a version creates a new latest version; it should not rewrite
  history.

## Goal

A goal is a business-scoped strategic outcome.

Core shape:

```ts
type Goal = {
  id: string
  workspaceId: string
  workspaceSlug: string
  businessId: string
  businessSlug: string
  title: string
  description: string | null
  status: "planned" | "active" | "achieved" | "canceled"
  level: string | null
  parentGoalId: string | null
  ownerAgentId: string | null
  targetDate: string | null
  achievedAt: string | null
  canceledAt: string | null
  createdAt: string
  updatedAt: string
}
```

Rules:

- Before creating an active goal, inspect existing goals for the business.
- Avoid duplicating an active goal that already captures the same outcome.
- Goal delete is a logical product delete, not a purge.

## Task

A task is the durable unit of work. It should be understandable by a fresh
agent without re-reading the entire chat.

Core shape:

```ts
type Task = {
  id: string
  title: string
  description: string | null
  status: "backlog" | "todo" | "in_progress" | "in_review" | "done" | "canceled"
  priority: "critical" | "high" | "medium" | "low"
  businessId: string
  businessSlug: string
  workspaceId: string
  workspaceSlug: string
  assigneeKind: "agent" | "user" | "unassigned"
  assigneeId: string | null
  labelIds: string[]
  labels: Label[]
  isBlocked: boolean
  relationships: TaskRelationships
  dueAt: string | null
  availableAfter: string | null
  parentTaskId: string | null
  createdAt: string
  updatedAt: string
  completedAt: string | null
  canceledAt: string | null
}

type TaskRelationships = {
  blockedBy: TaskRelationship[]
  blocking: TaskRelationship[]
  relatedTo: TaskRelationship[]
}
```

Read-only fields include `id`, workspace/business IDs and slugs, timestamps,
completion/cancel timestamps, derived labels, and derived relationship fields.

Fields to usually ignore unless the task is about runtime internals:

- `activeClaimId`, `claimedByAgentId`, `claimExpiresAt`
- `availabilityState`
- `executionPolicy`, `executionState`
- `metadataJson`

## Task Operating Model

### Good Task Shape

Use descriptions for stable pre-work context:

```markdown
**Context:**
<why this work matters and how it ties to the active goal>

**References:**
- <brief/goal/task/comment/file links>

**Scope:**
- In: <included work>
- Out: <excluded work>

**Done when:**
- <observable completion condition>

**Review needed:**
- <reviewer, artifact, decision, and next step>
```

Rules:

- Write titles as outcomes, not vague activities.
- Use actual newline characters; do not write literal `\n`.
- Use comments for progress and decisions instead of repeatedly rewriting the
  description.
- Use `in_review` only when there is a real reviewer, artifact, decision, or
  approval path.

### Duplicate Avoidance

Before creating a task:

1. Identify the business and active goal.
2. Search open tasks by business, status, and likely keywords.
3. Search recently completed tasks when continuing old work is plausible.
4. View likely matches with comments, events, children, relationships, and
   artifacts.
5. Update an existing task when the work is an iteration on an active
   deliverable.
6. Create a new task only when no active or recent task fits.

### Parent And Child Tasks

Use a parent task for a meaningful deliverable, active goal step, or focused
work package.

Use child tasks for bounded execution units, review steps, specific artifact
variants, distinct dependencies, or grouped feedback from a long user message.

Prefer comments over child tasks when the update is only progress, a decision,
or a small correction that does not need independent tracking.

### Mutation Hygiene

Every meaningful task mutation should leave a durable, user-readable trail.

For task writes:

1. Re-read the task with comments, events, children, relationships, and
   artifacts.
2. Decide whether the write is start, progress, description repair, child
   creation, blocker change, handoff, review request, or completion.
3. Add one clear comment when the change affects scope, review, blockers,
   output artifacts, readiness, or future context.
4. Attach produced files to the relevant comment and verify the attachments.
5. Apply status, relationship, or child mutations.
6. Read back the task before reporting success.

Do not create one comment per mechanical CLI/API write. Batch related changes
into one useful progress, review, blocker, or completion comment.

### Review Handoff

Before moving a task to `in_review`, add a concise review request comment and
attach reviewable artifacts to that comment.

Use this shape:

```markdown
Review request: <short outcome>

What changed:
- ...

Please review:
- <artifact/file/link>
- <specific decision or quality bar>
```

Move to `in_review` only after artifact upload and verification pass.

### Completion Handoff

Before marking `done`, add a final comment when the task produced durable
context another worker may need.

Use this shape:

```markdown
Done: <short outcome>

Completed:
- ...

Files/artifacts:
- ...

Notes for next task:
- ...
```

Do not report work complete or ready for review until required attachments are
visible through Vertical.

## Task Relationship

Task relationships model blockers and related work.

Core shape:

```ts
type TaskRelationship = {
  id: string
  source: { entityType: "task"; id: string }
  target: { entityType: "task"; id: string }
  type: "blocks" | "related"
  reason: string | null
  resolvedAt: string | null
}
```

Rules:

- `blocked` is not a task status.
- Blocked state is derived from active `blocks` relationships.
- For `blocks`, the source task blocks the target task.
- `related` is neutral, even though it still has a stored source and target.
- Remove or resolve a blocker only when the dependent task is actually ready.

## Comment

Comments are append-first execution history and discussion on tasks or CRM
records.

Core shape:

```ts
type Comment = {
  id: string
  ownerType: "task" | "contact" | "organization"
  ownerId: string
  bodyMarkdown: string
  status: "active" | "deleted"
  supersedesCommentId: string | null
  createdAt: string
  updatedAt: string
  deletedAt: string | null
}
```

Rules:

- Use comments for progress, decisions, review requests, and handoffs.
- Corrections create a corrected record and link to the superseded comment.
- Deletes are logical/status-based.
- Task comments can trigger task/runtime behavior; CRM comments are CRM-scoped
  history and do not enqueue task runtime wakes.

## Contact

A contact is a business-scoped CRM person.

Core shape:

```ts
type Contact = {
  id: string
  workspaceId: string
  workspaceSlug: string
  businessId: string
  businessSlug: string
  organizationId: string | null
  fullName: string
  normalizedFullName: string
  description: string | null
  title: string | null
  primaryEmail: string | null
  primaryPhone: string | null
  linkedinUrl: string | null
  stage: ContactStage
  firstContactedAt: string | null
  lastContactedAt: string | null
  lastRepliedAt: string | null
  mergedIntoContactId: string | null
  sourceSummaryJson: Record<string, unknown> | null
  metadataJson: Record<string, unknown> | null
  deletedAt: string | null
  createdAt: string
  updatedAt: string
}

type ContactStage =
  | "sourced"
  | "enriched"
  | "qualified"
  | "contacted"
  | "engaged"
  | "meeting"
  | "opportunity"
  | "customer"
  | "disqualified"
  | "closed_lost"
```

Rules:

- Contacts do not expose a public lifecycle/status field.
- Use `stage` for CRM progression.
- Active email aliases are unique per workspace/business.
- A contact can optionally belong to one organization.
- The organization must be active and in the same workspace/business.
- Deleted and merged contacts are excluded from normal reads.

## Organization

An organization is an external CRM account/company. It is not a Vertical
business.

Core shape:

```ts
type Organization = {
  id: string
  workspaceId: string
  workspaceSlug: string
  businessId: string
  businessSlug: string
  name: string
  normalizedName: string
  description: string | null
  domain: string | null
  websiteUrl: string | null
  linkedinUrl: string | null
  contactCount: number
  mergedIntoOrganizationId: string | null
  metadataJson: Record<string, unknown> | null
  deletedAt: string | null
  createdAt: string
  updatedAt: string
}
```

Rules:

- Public noun: `organization`, not `company`.
- Organizations do not expose a public lifecycle/status field.
- Active normalized names are unique per workspace/business.
- Active domain aliases are unique per workspace/business.
- An organization can have many contacts.
- Deleting an organization does not delete its contacts.
- Deleted and merged organizations are excluded from normal reads.

## File

A file is uploaded bytes plus metadata. It is private/authenticated and not
visible on its own.

Core shape:

```ts
type FileObject = {
  id: string
  workspaceId: string
  businessId: string | null
  originalFilename: string
  contentType: string
  byteSize: number
  checksum: string | null
  createdAt: string
  deletedAt: string | null
}
```

Rules:

- A file becomes visible only through an attachment or inline placement.
- Delete physical file bytes only when unused.

## Attachment

An attachment is a visible resource on another object.

Core shape:

```ts
type Attachment = {
  id: string
  fileId: string
  ownerType: "task" | "comment" | "goal" | "business" | "contact" | "organization"
  ownerId: string
  displayName: string | null
  createdAt: string
  deletedAt: string | null
}
```

Rules:

- Attachment IDs look like `att_...`.
- Mentioning a filename in markdown is not an attachment.
- Deleting an attachment removes the visible placement, not necessarily the
  underlying file bytes.

## Inline File

An inline file is a file reference inside markdown content.

Core shape:

```ts
type InlineFile = {
  id: string
  fileId: string
  ownerType: "task" | "comment" | "goal" | "business-brief" | "organization"
  ownerId: string
  contentField: "description" | "bodyMarkdown"
  createdAt: string
}
```

Rules:

- Inline placement IDs look like `ref_...`.
- Inline files are created when markdown containing the file reference is saved
  into the owner field.

## Label

A label is a business-scoped task classifier.

Core shape:

```ts
type Label = {
  id: string
  businessId: string
  name: string
  color: string
  status: "active" | "archived"
  createdAt: string
  updatedAt: string
}
```

Rules:

- Active label names are case-insensitively unique per business.
- Archived labels remain visible on historical tasks but should not be newly
  attached.

## User

A user is a durable human identity in a workspace.

Core shape:

```ts
type User = {
  id: string
  displayName: string
  email: string | null
  createdAt: string
  updatedAt: string
}
```

Rules:

- Users can own OAuth grants and API credentials.
- Users can be assigned to tasks where the assignment contract allows it.

## Agent

An agent is a durable delegated identity in a workspace.

Core shape:

```ts
type Agent = {
  id: string
  displayName: string
  status: "active" | "disabled"
  createdAt: string
  updatedAt: string
}
```

Rules:

- Agent API/OAuth credentials act as the agent principal.
- Use explicit agent credentials for automation rather than sharing user
  credentials.

## Event

An event is durable change history for workspace objects.

Core shape:

```ts
type Event = {
  id: string
  workspaceId: string
  businessId: string | null
  entityType: string
  entityId: string
  eventType: string
  payload: Record<string, unknown>
  occurredAt: string
}
```

Rules:

- Use events to inspect change history.
- Event time filters apply to `occurredAt`.
