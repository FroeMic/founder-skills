# Founder Skills

Small, composable agent skills for founders building vertical software companies.

These skills are meant to be practical working habits, not a large operating
system. Run a setup skill once in a project, then use the focused skills when a
specific founder workflow comes up.

## Quickstart

Install the skills into a project with your agent or with a skills installer:

```bash
npx skills@latest add FroeMic/founder-skills
```

Then run the setup skill for the area you want to use first:

```text
/setup-gtm-skills
```

Runtime notes, drafts, and project-specific context should live in the target
project under `.vertical/`. This source repo intentionally does not contain user
data.

## Why These Skills Exist

Founders switch between product, engineering, sales, marketing, and operations
all day. General agents can help, but they need a small amount of durable
process and context to avoid repeatedly rediscovering the same facts.

The goal of this repo is to make that durable process explicit:

- founder context stays in the project where the work happens
- skills stay small enough to inspect and edit
- workflows are composable across vertical, engineering, productivity, and GTM
- persistence goes into `.vertical/`, not into this source repo

## Repository Layout

```text
vertical/
  setup-vertical/
  vertical-onboard/
  vertical/
engineering/
  setup-engineering-skills/
  grill-with-docs/
  to-issue/
productivity/
  setup-productivity-skills/
  grill-me/
  handoff/
go-to-market/
  setup-gtm-skills/
  product-marketing/
  prospecting/
  email-sequence-design/
  cold-email/
  competitor-research/
  content-strategy/
  marketing-ideas/
  social-content/
```

## Starter GTM Set

The first usable skills focus on a simple building-in-public content loop:

- [`setup-gtm-skills`](go-to-market/setup-gtm-skills/SKILL.md) creates the
  `.vertical/gtm` workspace in the project using the skills.
- [`product-marketing`](go-to-market/product-marketing/SKILL.md) captures the
  product, audience, positioning, proof, and voice.
- [`competitor-research`](go-to-market/competitor-research/SKILL.md) profiles
  competitors from URLs and stores raw research plus synthesized profiles.
- [`content-strategy`](go-to-market/content-strategy/SKILL.md) turns context and
  ideas into a lightweight content strategy and idea backlog.
- [`marketing-ideas`](go-to-market/marketing-ideas/SKILL.md) generates candidate
  building-in-public topics.
- [`social-content`](go-to-market/social-content/SKILL.md) turns selected ideas
  into platform-ready posts.

The minimal set copied from
[`coreyhaines31/marketingskills`](https://github.com/coreyhaines31/marketingskills)
is `product-marketing`, `content-strategy`, `marketing-ideas`, and `social`
renamed here as `social-content`. Their referenced support files and evals are
included. The only repo-specific changes are the `.vertical/gtm/` persistence
path and the `social-content` skill name. That set is enough to capture
positioning, decide what to publish, generate ideas, store selected backlog
items, and draft social posts without importing a full marketing operating
system.

`competitor-research` is copied from the upstream `competitor-profiling` skill,
with output paths patched to `.vertical/gtm/competitor-profiles/`.

## Installation

See [`INSTALL.md`](INSTALL.md) for an agent prompt that Claude, Cursor, Codex, or
another coding agent can follow to install this repo into a project.

## License

MIT. See [`LICENSE.md`](LICENSE.md).
