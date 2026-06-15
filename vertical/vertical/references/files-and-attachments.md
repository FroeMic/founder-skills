# Vertical Files And Attachments Reference

Use this reference when uploading, attaching, listing, downloading, removing,
linking, or verifying files on Vertical objects.

## Mental Model

- `file`: uploaded bytes plus metadata. Private/authenticated and not visible by
  itself.
- `attachment`: visible resource on an object. Attachment IDs look like
  `att_...`.
- `inline file`: file referenced inside markdown content. Inline placement IDs
  look like `ref_...`.
- `placement`: either an attachment or inline reference. Deep links target
  placements, not bare files, because one file can appear in multiple places.

Mentioning a filename in markdown or comment text is not an attachment. Upload
and verify through Vertical before claiming a file is visible.

## CLI Setup

For production API-token auth:

```sh
export VERTICAL_API_URL="https://startwithvertical.com"
export VERTICAL_API_TOKEN="<token>"
vertical auth probe --json
```

If a managed workspace provides `./bin/vertical`, prefer that wrapper.

## Supported Owners

Visible attachment owners:

```text
task:<tsk_id>
comment:<cmt_id>
goal:<gol_id>
business:<biz_id_or_slug>
contact:<cnt_id>
organization:<org_id>
```

Inline markdown owners:

```text
task:<tsk_id>              # task description
comment:<cmt_id>           # comment bodyMarkdown
goal:<gol_id>              # goal description
business-brief:<biz_id>    # business brief/description
organization:<org_id>      # organization description
```

Use `business:<businessIdOrSlug>` for visible business resources and
`business-brief:<businessId>` for inline files inside the business brief.

## Upload A File Only

```sh
vertical files upload ./diagram.png --business <businessIdOrSlug> --json
```

This returns a `file_...` ID plus markdown helpers. The file is private and not
shown anywhere until attached or placed inline.

## Visible Attachments

Attach a new file directly:

```sh
vertical attachments upload --owner task:tsk_123 ./diagram.png --json
vertical attachments upload --owner comment:cmt_123 ./trace.md --json
vertical attachments upload --owner contact:cnt_123 ./notes.pdf --json
vertical attachments upload --owner organization:org_123 ./account-plan.md --json
```

Attach an existing file:

```sh
vertical attachments add-file --owner task:tsk_123 file_123 --json
```

List and delete attachments:

```sh
vertical attachments list --owner task:tsk_123 --json
vertical attachments list --owner comment:cmt_123 --json
vertical attachments delete att_123 --json
```

Deleting an attachment removes the visible placement, not the underlying file
bytes.

After attaching to a comment, verify before claiming success:

```sh
vertical attachments list --owner comment:cmt_123 --json
```

## Inline Files In Markdown

`files inline upload` uploads the file and returns markdown. The durable inline
reference is created when that markdown is saved into the owner field.

Task description example:

```sh
vertical files inline upload \
  --owner task:tsk_123 \
  --field description \
  ./diagram.png \
  --json

vertical tasks update tsk_123 \
  --description "See this: ![diagram](https://uploads.../file_123/content)" \
  --json
```

Comment body example:

```sh
vertical files inline upload \
  --owner comment:cmt_123 \
  --field bodyMarkdown \
  ./trace.md \
  --json

vertical comments correct --task tsk_123 cmt_123 \
  --body "Updated body with [trace](https://uploads.../file_456/content)" \
  --json
```

Normalize markdown before saving when you want the exact persisted markdown:

```sh
vertical files inline normalize \
  --owner task:tsk_123 \
  --field description \
  --body-file ./description.md \
  --json
```

Verify inline files:

```sh
vertical files inline list --owner task:tsk_123 --field description --json
vertical files inline list --owner comment:cmt_123 --field bodyMarkdown --json
```

## Read And Download Files

```sh
vertical files get file_123 --json
vertical files download file_123 ./diagram.png --json
vertical files download file_123 ./diagram.png --force --json
vertical files placements file_123 --json
vertical files delete file_123 --yes --json
```

Delete file bytes only when unused. If the file still has live attachments or
inline references, the API should return conflict.

## Where Files Appear On Reads

- Task detail includes direct task attachments and inline files from
  `description`.
- Task comments include direct comment attachments and inline files from
  `bodyMarkdown`.
- Goal detail includes direct goal attachments and inline files from
  `description`.
- Business detail includes direct business attachments and inline files from the
  business brief/description.
- Contact detail can include contact attachments.
- Organization detail can include direct organization attachments and inline
  files from `description`.
- List endpoints generally do not hydrate all file details unless explicitly
  documented.

## Deep Links

Use deep links for user-facing references to tasks, comments, goals,
attachments, and inline files:

```sh
vertical deep-links task tsk_123 --json
vertical deep-links comment cmt_123 --json
vertical deep-links goal gol_123 --json
vertical deep-links placement att_123 --json
vertical deep-links placement ref_123 --open --json
vertical files placements file_123 --json
```

Placement deep links return `deepLinkUrl` and sometimes `openDeepLinkUrl`.
`open=1` only matters for previewable files such as images and markdown.

## Task Artifact Rule

Files created while executing a task are execution outputs. Attach them to the
relevant task comment, not silently to the task itself.

Before moving a task to `in_review` or `done`:

1. Add the review/completion comment.
2. Upload every reviewable artifact to that comment.
3. Verify with `vertical attachments list --owner comment:<cmt_id> --json`.
4. Only then change the task status.
