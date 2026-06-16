# Respondent Interview Operator Standards

Use these standards when compiling or revising a respondent-facing interview skill.

## Interview Goal

The respondent skill exists to understand demand reality, not to sell the product.

Good output answers:

- Is this respondent actually in the configured ICP?
- What project are they trying to make progress on?
- What makes the problem unavoidable, if anything?
- What options do they use today?
- Where do those options fail?
- Does the founder's offer fit demand after PULL is understood?

## Interview Posture

Be direct, warm, and non-salesy. The respondent should feel listened to, not qualified for a pitch.

Use question families, not rigid scripts. Follow concrete incidents over the prepared order. If the respondent gives a real recent example, stay with it until the workflow, consequence, and options are clear.

Never treat politeness as validation.

## ICP Fit Paths

Classify the respondent before running the full interview:

```text
Strong fit
  Continue full PULL interview.

Adjacent fit
  Run abbreviated PULL.
  Label the report as adjacent segment evidence.
  Do not count it as primary ICP validation.

Weak / no fit
  Stop full interview.
  Ask 2-3 routing questions.
  Write a non-fit report.
```

Routing questions for weak fit:

- Who in your world does run into this problem often?
- What role or situation would be closer to this workflow?
- Is there any circumstance where this problem would become relevant to you?

Rule:

```text
No ICP fit means no demand validation.
```

It can still produce useful learning about screening, language, and adjacent segments.

## PULL Question Families

### Project

Goal: understand what progress the respondent is trying to make.

Use questions like:

- Walk me through the last time this came up.
- What were you trying to get done?
- What outcome mattered in that moment?
- What were you responsible for?

Push until the answer is a concrete project, not a category-level complaint.

### Unavoidable

Goal: understand urgency, stakes, and consequences.

Use questions like:

- What happens if this does not get solved?
- Why did it matter then?
- Who noticed or would have noticed?
- What gets worse if this keeps happening?
- What made this a now problem instead of background annoyance?

Red flag: the respondent says it is annoying but cannot name a consequence.

### List of Options

Goal: map what they actually do today.

Use questions like:

- What did you use or do last time?
- What have you tried before?
- What would you do if this happened tomorrow?
- What does doing nothing look like?
- Did you consider hiring, delegating, buying, or building something?

Always include "do nothing" as an option if it is plausible.

### Limitations

Goal: understand why current options fail or remain tolerated.

Use questions like:

- Where does the current workaround break?
- What still takes too long?
- What do you keep tolerating?
- What is the most annoying part you no longer notice because it is normal?
- What would make switching obvious?

Push for behavioral limitations, not adjectives like "clunky" or "inefficient" without examples.

## Forbidden Weak Questions

Do not use these as primary evidence:

- Would you use this?
- Would you pay for this?
- Is this interesting?
- Does this sound useful?
- Would this save you time?

If a similar question is asked during offer fit, label it as reaction, not demand evidence.

## Supply Reveal Rules

Default order:

```text
1. PULL demand discovery
2. Offer smallest supply
3. See if it fits
4. Address fit questions
```

Do not reveal the offer before PULL unless the config says `reveal_mode: never` or the respondent directly asks and refusing would break trust. If they ask early, say:

```text
I can share the concept, but first I want to understand how this works for you today so I do not lead your answers. Two more questions, then I will show the smallest version.
```

The supply reveal should be the smallest possible description or demo that helps evaluate fit. Avoid product tours.

## Demand Reality Score

Score 1-10. Explain the score in one paragraph.

Strong evidence:

- Recent specific incident.
- Recurring frequency.
- Current workaround.
- Clear consequence.
- Money, time, headcount, or reputation already spent.
- Clear owner or buyer.
- Active search or attempted switch.
- Respondent asks for follow-up, access, or introduction.

Weak evidence:

- Hypothetical interest.
- No recent example.
- No workaround.
- No consequence.
- Problem belongs to someone else.
- Likes the offer only after the reveal.
- Not ICP-fit.

Suggested scoring:

```text
9-10: acute, recent, repeated, costly, owned, with poor alternatives.
7-8: real repeated pain and workaround, but buyer/timing/switching still uncertain.
5-6: some pain, but weak urgency, weak consequence, or adjacent ICP.
3-4: mostly hypothetical or mild annoyance.
1-2: no fit, no problem, or no current behavior.
```

## Required Report Sections

Every report must include:

```markdown
# Interview Report

## ICP Fit
Strong / Adjacent / Weak

## PULL Summary
### Project
Status: Observed / Inferred / Missing

### Unavoidable
Status: Observed / Inferred / Missing

### List of Options
Status: Observed / Inferred / Missing

### Limitations
Status: Observed / Inferred / Missing

## Demand Reality
Score: N/10
Reason:

## Evidence
Recent incident:
Current workaround:
Cost / consequence:
Owner / buyer:
Switch trigger:

## Strong Quotes

## Red Flags

## Offer Fit
Shown: yes/no
Reaction:
Objections:
Next-step signal:

## Hypotheses Updated

## Recommendation
Continue / narrow ICP / revise problem / stop
```

Also produce a cleaned `transcript.md` or interview notes appendix and a small `meta.json` when possible.
