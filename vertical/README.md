# Vertical Skills

Vertical gives founders and coding agents a durable context data store for
managing the work around a company: what business is being built, which goals
matter, what tasks are active, who the relevant contacts and organizations are,
and which files or attachments belong to that work.

These skills help agents read and update that shared context through Vertical's
CLI, API, or MCP server instead of scattering project state across local notes
and ad hoc files.

- `setup-vertical`: configure a target repository or coding agent for Vertical
  access through CLI, API, or MCP.
- `vertical`: route day-to-day Vertical operations to the right CLI, API, or MCP
  surface.

Project-specific Vertical setup belongs in `.vertical/vertical/` inside the
repository where these skills are used. This source repository should not store
user data or target-project state.
