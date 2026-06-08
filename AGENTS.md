# Agent Instructions

This repository contains source skills only. Do not store project-specific user
data here.

## Persistence

- Skills that need persistence must write it to `.vertical/` in the repository
  where the skill is being used.
- Do not create or commit `.vertical/` in this source repository.
- When adding a new skill, include the canonical `.vertical/` paths it reads or
  writes.

## Git

- Use merge commits when integrating branches.
- Make small and frequent commits.
- Keep commits scoped to one coherent change.
- Do not rewrite history unless the repository owner explicitly asks for it.

## Skill Structure

- A skill folder should contain a concise `SKILL.md`.
- Put long optional references in `references/` and load them only when needed.
- Avoid adding per-skill README files unless the folder is not an actual skill.
- Prefer adapting existing repo style over introducing new conventions.
