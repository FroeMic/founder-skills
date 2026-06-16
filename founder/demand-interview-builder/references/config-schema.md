# Demand Interview Builder Config Schema

Use this schema when writing `.vertical/demand-interview-builder/projects/<project-slug>/configs/vNNN.yaml`.

Every config file is a complete snapshot of one interview-skill version. The changelog lives inside the config, so the project history is the ordered set of config files.

## Required Shape

```yaml
version: "v001"
supersedes: null
created_at: "2026-06-16T12:00:00Z"
project:
  slug: "client-followup-ai"
  name: "Client follow-up AI"
  status: "active"

changelog:
  summary: "Initial demand interview setup."
  changed:
    - field: "initial"
      from: null
      to: "v001"
      why: "Created the first respondent interview skill for this idea space."

idea_space:
  one_liner: "Help independent consultants avoid dropping client follow-ups."
  testing_now:
    - "Whether missed follow-up is an urgent enough problem."
    - "Whether consultants already use painful workarounds."
  not_testing_now:
    - "Pricing page copy."
    - "Full CRM replacement."
  founder_hypotheses:
    - "Consultants with many active clients have stronger pain than general freelancers."

icp:
  label: "Solo RevOps consultant with retained clients"
  real_person_exemplar: "Anonymized: solo RevOps consultant with 8 active clients and 10+ calls/week."
  screeners:
    role_or_responsibility: "Owns client delivery and follow-up."
    situation: "Runs multiple client meetings weekly and tracks commitments personally."
    frequency_or_recency: "5+ client calls/week or a missed/near-missed follow-up in the last 30 days."
    trigger: "Client commitments are spread across notes, email, Slack, and task tools."
    ownership: "User and economic buyer."
  disqualifiers:
    - "Has a dedicated project manager handling follow-up."
    - "Uses a CRM rigorously and trusts it."
    - "Has fewer than 2 client calls/week."
  adjacent_segments:
    - "Small agency PMs who coordinate consultant follow-ups but do not buy tools."

pull:
  project:
    hypothesis: "Keep client commitments from falling through the cracks."
    existing_evidence: "Founder has heard consultants mention missed action items after calls."
    interview_goal: "Find the last real incident and the progress they were trying to make."
    red_flags:
      - "Respondent cannot name a recent incident."
  unavoidable:
    hypothesis: "Missed follow-ups damage trust, renewals, or referrals."
    existing_evidence: null
    interview_goal: "Discover consequences, urgency, and who notices."
    red_flags:
      - "The problem is annoying but consequence-free."
  list_of_options:
    hypothesis: "They currently use notes, inbox search, reminders, CRM, or doing nothing."
    existing_evidence: null
    interview_goal: "Map what they actually do today and why they keep doing it."
    red_flags:
      - "There is no current workaround and no active search."
  limitations:
    hypothesis: "Current options are scattered, manual, and easy to ignore."
    existing_evidence: null
    interview_goal: "Find where the workaround breaks and what makes switching obvious."
    red_flags:
      - "Current workflow is good enough and trusted."

offer:
  reveal_mode: "after-pull" # "never" | "after-pull"
  plain_language_label: "client commitment tracker"
  smallest_supply_reveal: "A lightweight system that turns client calls and notes into a reliable next-action list without forcing a full CRM migration."
  claims_not_allowed:
    - "This will save you hours every week."
    - "This uses AI to solve follow-up."
  terms_to_avoid_before_pull:
    - "AI"
    - "automation"
    - "CRM replacement"
  fit_questions:
    - "What part of that would fit your current workflow?"
    - "What would make this not worth switching to?"

interview_rules:
  tone: "direct, warm, non-salesy"
  must_do:
    - "Ask one question at a time."
    - "Probe recent behavior before opinions."
    - "Defer product discussion until PULL is complete."
    - "Label weak or adjacent ICP fit clearly."
  must_not_do:
    - "Ask 'would you use this?' as primary evidence."
    - "Pitch before demand discovery."
    - "Count non-ICP answers as validation."

private_founder_notes:
  # Optional. Never compile this field into the respondent skill.
  - "Internal worries, strategy, or interpretations that would lead the respondent."

reporting:
  produce_report: true
  produce_transcript: true
  produce_meta_json: true
  reports_path_hint: ".vertical/demand-interview-builder/projects/client-followup-ai/reports/v001/"
  consent_line: "I will produce a short report and cleaned transcript/notes appendix for the founder. Please do not share confidential information."

quality_gate:
  status: "passed" # "passed" | "compiled-with-warning"
  warnings: []
```

## Changelog Rules

For `v001`, `supersedes` is `null` and the changelog says this is the initial config.

For later versions:

```yaml
version: "v003"
supersedes: "v002"
changelog:
  summary: "Narrowed the ICP and softened the offer language."
  changed:
    - field: "icp.label"
      from: "independent consultants"
      to: "solo RevOps consultants with 5+ retained clients"
      why: "v002 reports showed stronger pain among retained-client operators."
    - field: "offer.plain_language_label"
      from: "AI follow-up assistant"
      to: "client commitment tracker"
      why: "Respondents described the problem as dropped commitments; AI language led the witness."
```

Rules:

- Include the full new config, not a patch.
- Write `from`, `to`, and `why` for meaningful changes.
- If a change is based on interview reports, name the report/version in `why`.
- If a change is founder judgment, say that explicitly.

## Project Index

Maintain `.vertical/demand-interview-builder/projects/<project-slug>/project.json` as a convenience pointer:

```json
{
  "project": "client-followup-ai",
  "name": "Client follow-up AI",
  "current_version": "v003",
  "versions": ["v001", "v002", "v003"]
}
```

The index can be rebuilt from configs. Do not rely on it as the only history.

## Public vs Private Compile Rules

The compiled respondent skill is sendable. Treat it as public to the interview partner.

Allowed in compiled skill:

- Project name and config version.
- Idea-space summary.
- ICP screeners and disqualifiers.
- PULL hypotheses needed to guide the interviewer.
- Offer boundary and smallest supply reveal.
- Reporting requirements.

Never compile:

- `private_founder_notes`.
- Founder anxieties, strategy, or fundraising context.
- Claims the founder cannot substantiate.
- Any personal data about a real-person exemplar unless explicitly anonymized.
- Changelog rationale that would lead the respondent.

Template placeholder map:

```text
{{RESPONDENT_SKILL_NAME}} -> lowercase hyphenated skill name, usually <project-slug>-interview
{{PROJECT_SLUG}}          -> project.slug
{{PROJECT_NAME}}          -> project.name
{{CONFIG_VERSION}}        -> version
{{IDEA_SPACE_YAML}}       -> public idea_space block, indented two spaces
{{ICP_YAML}}              -> public icp block, indented two spaces
{{PULL_YAML}}             -> public pull block, indented two spaces
{{OFFER_YAML}}            -> public offer block, indented two spaces
{{REPORTING_YAML}}        -> public reporting block, indented two spaces
```
