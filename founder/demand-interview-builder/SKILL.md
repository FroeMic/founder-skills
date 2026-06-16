---
name: demand-interview-builder
description: "Use when the founder wants to create, revise, or generate customer interview or pre-PMF sales-call skills that another agent can run with an interview participant. Use for PULL-based user interviews, ICP specificity, demand validation, early sales calls, customer discovery, interview project versioning, or turning a startup idea into a sendable SKILL.md."
---

# Demand Interview Builder

Build versioned interview skills for customer conversations that test whether the problem is real. This skill interviews the founder, forces specificity, writes a full config snapshot, and generates a portable `SKILL.md` for interview participants. Generated skills can run pure discovery or pre-PMF sales calls; participant signal use separately marks whether the output counts as demand evidence, adjacent learning, or pilot-only learning.

Saved project history lives in the project where this skill is used:

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
The generated interview skill keeps the participant grounded in what actually happened.
The report makes the evidence auditable.
The call mode decides how far to go after PULL.
```

Push for concrete situations, recent behavior, current alternatives, consequences, and real next-step energy. Do not generate an interview skill from abstract ICPs or polished product claims.

Use Rob Snyder's PULL framework as the interview spine:

- **Project** — what progress the participant is trying to make.
- **Unavoidable** — why this is prioritized now, what would make it strange to defer, who notices, and what gets worse.
- **List of Options** — what they actually use or consider today, including doing nothing.
- **Limitations** — where those options break, become costly, or remain tolerated only because there is no better fit.

## Runtime Style

If the runtime has a structured question tool, use it for founder decisions that branch or commit a version. Otherwise ask in normal prose and wait.

Good decision gates:

- Create a new interview project or revise an existing one.
- Narrow an ICP now or generate with a warning.
- Run pure discovery or a pre-PMF sales call.
- Mark participant signal use separately as demand evidence, adjacent learning, or pilot-only learning.
- Include no offer, an offer fit check after PULL, or a close ask after supply-fit.
- Save the current setup as the next version.
- Generate the participant interview skill from the saved config.

The generated participant interview skill must be runtime-neutral. It should ask one conversational question at a time and must not require `AskUserQuestion`.

## Workflow

### Phase 0: Find or Create Project

Check `.vertical/demand-interview-builder/projects/`.

If projects exist, list them with current version and ask whether to revise one or create a new project. If no project exists, create a new slug from the startup idea, lowercase hyphenated.

Read the latest config before asking questions. Use it to skip already-set fields and focus on what changed.

### Phase 1: Demand Hypothesis

Start in normal language:

```text
Let's start with the Demand Hypothesis.

In one plain-English sentence: who should we interview first, and what problem or progress are we testing?
```

Capture the answer as `demand_hypothesis`.

Do not accept product copy. If the founder says "AI assistant for consultants", ask what is happening in the consultant's world when the problem appears.

Also capture boundaries:

- What is being tested now.
- What is explicitly not being tested.
- What the founder currently believes but is unsure about.

### Phase 2: Who Exactly

Force the ICP (ideal customer profile) into a situation you can check in a few questions, not a broad persona.

Minimum ICP fields:

- Role or responsibility.
- Situation or workflow they personally experience.
- Frequency or recency threshold.
- Trigger that makes the problem active now.
- Ownership: user, buyer, recommender, or adjacent observer.
- Disqualifiers.

Ask for one real person when possible. They may be anonymized, but the description should be based on one actual person or observed workflow.

If the founder names a cofounder, teammate, investor, or otherwise biased participant, support it only as a pilot run unless the founder explicitly says the signal should be treated differently. Record the participant as useful for testing the interview flow, not for demand validation.

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
- What the participant interview should discover.
- Red flags that would weaken the hypothesis.

If a field is weak, name it directly and ask a sharper follow-up. Do not smooth over gaps. Do not force the founder to invent the participant's consequence or urgency; it is valid to mark `unavoidable` as unknown and make that the thing the interview tests.

### Phase 4: Call Mode, Offer Fit, and Close Ask

Decide how far the participant skill should go after PULL.

Capture:

- Call mode: `pure_discovery` or `pre_pmf_sales`.
- Participant signal use: `demand_evidence`, `adjacent_learning`, or `pilot_only`.
- Opener style: warm role/current-work opening, why they took the call, agenda alignment, and light woven screening.
- Offer label in plain language.
- Smallest offer description or demo the participant skill may introduce after demand recap.
- Claims the participant skill must not make.
- Terms to avoid before PULL because they lead the witness.
- Offer fit mode: `never`, `after-demand-recap`, or `yolo-demo-if-no-pull`.
- Close ask mode: `none`, `next_step`, `paid_pilot`, or `purchase`.
- Exact ask text or the smallest honest commitment to request.

Default: `pre_pmf_sales` with offer fit after demand recap, but no close ask unless the founder can name a concrete next step or paid/purchase commitment. Never make a close ask before demand-fit and supply-fit.

### Phase 5: Reporting and Consent

Decide what the participant interview skill should return:

- `report.md` — synthesized PULL and demand reality.
- `transcript.md` — cleaned transcript or interview notes.
- `meta.json` — non-sensitive run metadata.

The participant interview skill must disclose this at the start of the interview and tell the participant not to share confidential information.

First version default: no remote telemetry. Local `meta.json` is fine.

### Phase 6: Quality Gate

Before writing a version, run this gate:

```text
ICP can be checked in 3-5 questions.
ICP includes situation, trigger, and disqualifiers.
PULL has at least one concrete hypothesis per field, or an explicit unknown-to-test label.
Current options include doing nothing.
Limitations are behavioral, not abstract.
Call mode and participant signal use are explicit.
Offer fit check happens only after PULL and demand recap, unless `yolo-demo-if-no-pull` is explicitly configured.
Close ask, if enabled, has a concrete ask and requires demand-fit plus supply-fit.
Pilot-only participants are not counted as demand validation.
Report format includes PULL, demand score, offer fit, close outcome, quotes, red flags, and transcript.
```

If the setup fails, do not generate the interview skill silently. Tell the founder what is missing and ask the next highest-leverage question.

If the founder insists on generating it anyway, allow that only with an explicit warning in the config:

```yaml
quality_gate:
  status: "generated-with-warning"
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
- `demand_hypothesis`
- `icp`
- `pull`
- `call`
- `offer`
- `interview_rules`
- `reporting`
- `quality_gate`

Also update `project.json` as a convenience index only. The versioned configs are the source of truth.

### Phase 8: Generate Participant Interview Skill

Before generating the interview skill, read:

- `references/interview-operator.md`
- `assets/participant-interview-template.md`

Generate the interview skill by replacing the template placeholders with sanitized public config from the saved version. Do not include private founder notes, internal doubts, or strategy that would lead the participant.

Write:

```text
.vertical/demand-interview-builder/projects/<project-slug>/compiled/vNNN/SKILL.md
```

After generating it, tell the founder:

- Config path.
- Compiled skill path.
- What to send to the interview participant.
- Where returned reports should be stored.

### Phase 9: Optional Report Synthesis

If reports already exist under `reports/vNNN/`, synthesize them only after reading the config version that generated them. Do not mix reports across versions without labeling the version split.

Useful synthesis output:

- Which PULL hypotheses strengthened or weakened.
- Which ICP screeners need tightening.
- Exact participant language to carry forward.
- Recommended next config change.

## Important Rules

- Ask one founder question at a time when collecting setup.
- Prefer recent behavior over opinions.
- Do not generate from ICPs that cannot be checked in a few questions without an explicit warning.
- Do not let a late offer fit check contaminate demand discovery.
- Do not ask for a purchase, paid pilot, or next step unless demand-fit and supply-fit are both present.
- If there is no PULL, the participant skill should make that safe, route to a better ICP, and record the miss instead of rescuing the hypothesis.
- Keep version history append-only. New understanding creates a new config version.
- Preserve uncertainty. Use `observed`, `inferred`, and `missing` labels in reports.
