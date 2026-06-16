---
name: demand-interview-builder
description: "Use when the founder wants to create, revise, or compile respondent-facing customer interview skills for early demand discovery. Use for PULL-based user interviews, ICP specificity, demand validation, customer discovery, interview project versioning, or turning an idea-space into a sendable SKILL.md that another agent can run with an interview participant."
---

# Demand Interview Builder

Build versioned, respondent-facing interview skills that test demand reality. This skill interviews the founder, forces specificity, writes a full config snapshot, and compiles a portable `SKILL.md` for interview participants.

The durable state lives in the project where this skill is used:

```text
.vertical/demand-interview-builder/
└── projects/
    └── <project-slug>/
        ├── project.json
        ├── configs/
        │   ├── v001.yaml
        │   └── v002.yaml
        ├── compiled/
        │   ├── v001/SKILL.md
        │   └── v002/SKILL.md
        └── reports/
            ├── v001/
            └── v002/
```

Do not store project-specific interview data inside the source `founder-skills` repository unless that repository is the user's active project.

## Operating Posture

Act like a demanding customer-discovery partner, not a form filler. The founder is allowed to be early, but not vague.

Core standard:

```text
The builder skill makes the founder specific.
The compiled skill makes the respondent behavioral.
The report makes the evidence auditable.
```

Push for concrete situations, recent behavior, current alternatives, and consequences. Do not compile an interview skill from abstract ICPs or polished product claims.

Use Rob Snyder's PULL framework as the demand-side spine:

- **Project** — what progress the respondent is trying to make.
- **Unavoidable** — why this matters now, what happens if it is not solved, who notices, and what gets worse.
- **List of Options** — what they actually use or consider today, including doing nothing.
- **Limitations** — where those options break, become costly, or remain tolerated only because there is no better fit.

## Runtime Style

If the runtime has a structured question tool, use it for founder decisions that branch or commit a version. Otherwise ask in normal prose and wait.

Good decision gates:

- Create a new interview project or revise an existing one.
- Narrow an ICP now or compile with a warning.
- Keep the respondent interview pure discovery or reveal the offer after PULL.
- Save the current setup as the next version.
- Compile the respondent skill from the saved config.

The compiled respondent skill must be runtime-neutral. It should ask one conversational question at a time and must not require `AskUserQuestion`.

## Workflow

### Phase 0: Find or Create Project

Check `.vertical/demand-interview-builder/projects/`.

If projects exist, list them with current version and ask whether to revise one or create a new project. If no project exists, create a new slug from the idea-space, lowercase hyphenated.

Read the latest config before asking questions. Use it to skip already-set fields and focus on what changed.

### Phase 1: Idea Space

Capture one plain-English sentence:

```text
We are exploring whether <specific people> need help with <job / problem / progress>.
```

Do not accept product copy as the idea space. If the founder says "AI assistant for consultants", ask what demand-side situation it is meant to help with.

Also capture boundaries:

- What is being tested now.
- What is explicitly not being tested.
- What the founder currently believes but is unsure about.

### Phase 2: ICP Specificity

Force the ICP into a screenable situation, not a broad persona.

Minimum ICP fields:

- Role or responsibility.
- Situation or workflow they personally experience.
- Frequency or recency threshold.
- Trigger that makes the problem active now.
- Ownership: user, buyer, recommender, or adjacent observer.
- Disqualifiers.

Require one real-person exemplar when possible. It may be anonymized, but it should be based on one actual person or observed workflow.

Pushback patterns:

```text
"marketing teams" -> what kind, at what company stage, doing what workflow, under what trigger?
"founders" -> pre-seed, seed, bootstrapped, agency founder, solo technical founder? What situation?
"they waste time" -> doing what, how often, with what consequence?
"everyone has this" -> name the first person you would interview and why them.
```

### Phase 3: PULL Hypothesis

Interview the founder until each PULL field is concrete enough to test.

For each field, capture:

- Founder hypothesis.
- Evidence the founder already has, if any.
- What the respondent interview should discover.
- Red flags that would weaken the hypothesis.

If a field is weak, name it directly and ask a sharper follow-up. Do not smooth over gaps.

### Phase 4: Offer Boundary

Define the supply side only after demand-side hypotheses are clear.

Capture:

- Offer label in plain language.
- Smallest supply reveal the respondent skill may show after PULL.
- Claims the respondent skill must not make.
- Terms to avoid because they lead the witness.
- Whether to run pure discovery only or include a late fit check.

Default: do not reveal the offer until PULL is complete.

### Phase 5: Reporting and Consent

Decide what the respondent-facing skill should return:

- `report.md` — synthesized PULL and demand reality.
- `transcript.md` — cleaned transcript or interview notes.
- `meta.json` — non-sensitive run metadata.

The respondent skill must disclose this at the start of the interview and tell the participant not to share confidential information.

First version default: no remote telemetry. Local `meta.json` is fine.

### Phase 6: Quality Gate

Before writing a version, run this gate:

```text
ICP is screenable in 3-5 questions.
ICP includes situation, trigger, and disqualifiers.
PULL has at least one concrete hypothesis per field.
Current options include doing nothing.
Limitations are behavioral, not abstract.
Offer reveal is bounded and comes after PULL.
Report format includes PULL, demand score, quotes, red flags, and transcript.
```

If the setup fails, do not compile silently. Tell the founder what is missing and ask the next highest-leverage question.

If the founder insists on compiling anyway, allow it only with an explicit warning in the config:

```yaml
quality_gate:
  status: "compiled-with-warning"
  warnings:
    - "ICP trigger is still broad."
```

### Phase 7: Write Versioned Config

Before writing the config, read `references/config-schema.md`.

Create the next version under:

```text
.vertical/demand-interview-builder/projects/<project-slug>/configs/vNNN.yaml
```

Every config is a full snapshot. Never write "same as v002 except..." configs.

Each config includes:

- `version`
- `supersedes`
- `changelog`
- `idea_space`
- `icp`
- `pull`
- `offer`
- `interview_rules`
- `reporting`
- `quality_gate`

Also update `project.json` as a convenience index only. The versioned configs are the source of truth.

### Phase 8: Compile Respondent Skill

Before compiling, read:

- `references/interview-operator.md`
- `assets/respondent-skill-template.md`

Compile by replacing the template placeholders with sanitized public config from the saved version. Do not include private founder notes, internal doubts, or strategy that would lead the respondent.

Write:

```text
.vertical/demand-interview-builder/projects/<project-slug>/compiled/vNNN/SKILL.md
```

After compiling, tell the founder:

- Config path.
- Compiled skill path.
- What to send to the respondent.
- Where returned reports should be stored.

### Phase 9: Optional Report Synthesis

If reports already exist under `reports/vNNN/`, synthesize them only after reading the config version that generated them. Do not mix reports across versions without labeling the version split.

Useful synthesis output:

- Which PULL hypotheses strengthened or weakened.
- Which ICP screeners need tightening.
- Exact respondent language to carry forward.
- Recommended next config change.

## Important Rules

- Ask one founder question at a time when collecting setup.
- Prefer recent behavior over opinions.
- Do not compile from unscreenable ICPs without an explicit warning.
- Do not let a late offer-fit check contaminate demand discovery.
- Keep version history append-only. New understanding creates a new config version.
- Preserve uncertainty. Use `observed`, `inferred`, and `missing` labels in reports.
