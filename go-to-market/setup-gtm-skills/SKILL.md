---
name: setup-gtm-skills
description: Use when setting up Founder Skills go-to-market workflows in a repository. Creates the `.vertical/gtm` workspace, initializes founder-led content files, and ensures `.vertical/` is ignored so project-specific GTM data does not bleed into a skills source repository.
---

# Setup GTM Skills

Set up the target repository for founder-led GTM work.

## Workflow

1. Confirm you are in the repository where the skills will be used.
2. Ensure `.vertical/` is ignored in `.gitignore`.
3. Create this structure if missing:

```text
.vertical/
  gtm/
    product-marketing.md
    content-strategy.md
    content-ideas.md
    social-calendar.md
    posts/
```

4. Do not overwrite existing files. If a file exists, read it and preserve its
   content.
5. Seed missing files with the templates below.
6. Tell the user the next useful skill is `/product-marketing` if no context
   exists, otherwise `/content-strategy`.

## Templates

`product-marketing.md`:

```markdown
# Product Marketing Context

Last updated:

## Product

## Audience

## Problem

## Positioning

## Differentiation

## Proof

## Voice
```

`content-strategy.md`:

```markdown
# Content Strategy

Last updated:

## Goal

## Audience

## Content Pillars

## Cadence

## Distribution

## Review Rhythm
```

`content-ideas.md`:

```markdown
# Content Ideas

| Status | Idea | Pillar | Source | Angle | Next Step |
|--------|------|--------|--------|-------|-----------|
```

`social-calendar.md`:

```markdown
# Social Calendar

| Date | Platform | Post | Status | Link |
|------|----------|------|--------|------|
```
