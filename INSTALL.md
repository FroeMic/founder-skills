# Install Founder Skills

Use this prompt with Claude Code, Cursor, Codex, or another coding agent from the
root of the project where you want to use the skills.

```text
Install the Founder Skills repository into this project.

Source: vertical/founder-skills

Requirements:
- Prefer `npx skills@latest add vertical/founder-skills` if the skills
  installer is available.
- If the installer is unavailable, clone the source repo to a temporary
  directory and copy the skill folders into `.agents/skills/`, preserving their
  category paths.
- If this project uses Claude Code, also make the skills available from
  `.claude/skills/` using symlinks or copied folders.
- Do not copy any `.vertical/` directory from the source repository.
- Add `.vertical/` to this project's `.gitignore` if it is not already ignored.
- After installation, run `/setup-gtm-skills` if I am setting up the GTM content
  workflow.
```

If you are installing from a local checkout instead of GitHub, replace the source
line with the absolute path to this repository and ask the agent to copy from
that path.
