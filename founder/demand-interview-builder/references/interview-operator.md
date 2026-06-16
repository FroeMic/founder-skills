# Participant Interview Operator Standards

Use these standards when generating or revising a participant interview skill.

## Interview Goal

The participant interview skill exists to understand whether the problem is real. If configured as a pre-PMF sales call, it may also test whether the founder's offer fits and ask for a concrete next step or commitment.

Good output answers:

- Is this participant actually in the ICP from the config?
- What project are they trying to make progress on?
- What makes the problem unavoidable, if anything?
- What options do they use today?
- Where do those options fail?
- Does the founder's offer fit after PULL is understood?
- If a close ask was made, did the participant exert real energy toward a next step?

## Interview Posture

Be direct, warm, and non-salesy. The participant should feel listened to, not qualified for a pitch.

Use question families, not rigid scripts. Follow concrete incidents over the prepared order. If the participant gives a real recent example, stay with it until the workflow, consequence, and options are clear.

Never treat politeness as validation.

Make it easy for the participant to say there is no fit. A clean "not a priority" is useful evidence.

## Call Modes

Read `call.mode` and `call.participant_signal_use` before starting.

```text
pure_discovery
  Fill PULL, route if no fit, do not introduce the offer.

pre_pmf_sales
  Fill PULL first.
  Play back demand.
  If demand-fit exists, introduce the smallest offer.
  If supply-fit exists and close_ask is enabled, ask for the configured commitment.
```

If `call.participant_signal_use` is `pilot_only`, run the configured call mode normally, but mark all demand conclusions as pilot-only and do not count the participant as demand validation.

Never use a cofounder, teammate, investor, or financially aligned person as demand validation. They can still be useful when `participant_signal_use` is `pilot_only`.

## Opening And Light Screening

Prefer a warm opener over an interrogation:

- Ask about their role and what they are working on.
- Ask why they took the call or what would make the conversation useful.
- Align on a rough agenda.
- Ask permission to understand their context before discussing the offer.

Weave ICP checks into this opening. Do not spend the first several turns on qualification unless the config explicitly requests `screener_style: explicit`.

## ICP Fit Paths

Classify the participant before running the full interview:

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

Goal: understand what progress the participant is trying to make.

Use questions like:

- Walk me through the last time this came up.
- What were you trying to get done?
- What outcome mattered in that moment?
- What were you responsible for?

Push until the answer is a concrete project, not a category-level complaint.

### Unavoidable

Goal: understand priority, urgency, stakes, and consequences.

Use questions like:

- Is this something you are actually trying to change soon, or could it sit for six months?
- Why did it matter then instead of staying a background annoyance?
- Who noticed or would have noticed?
- What gets worse if this keeps happening?
- What made this a now problem instead of background annoyance?

Red flag: the participant says it is annoying but would not prioritize changing it.

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

## Demand Recap

Before introducing any offer, play back the participant's PULL:

```text
It sounds like you are trying to accomplish <project>, because <unavoidable>, and today you are considering or using <options>, but those break because <limitations>. What am I missing?
```

If important fields are missing, label them as missing and ask one follow-up. If PULL still does not exist, do not rescue the hypothesis. Route, end, or run `yolo-demo-if-no-pull` only if explicitly configured.

## Offer Fit Check

Default order:

```text
1. PULL demand discovery
2. Demand recap
3. Introduce the smallest offer
4. See if it fits
5. Address fit questions
```

Do not introduce the offer before PULL. If the config says `call.offer_fit.mode: never`, do not introduce it at all. If the participant asks early and refusing would break trust, say:

```text
I can share the concept, but first I want to understand how this works for you today so I do not lead your answers. Two more questions, then I will introduce the smallest version.
```

The offer description should be the smallest possible description that fits the participant's PULL. Use this shape when the config gives enough information:

```text
We are a <plain_language_label> that helps <ICP> do <project> without <limitations>.
```

Then describe their unblocked life using `offer.unblocked_life_description`, if present. Keep the description to `call.offer_fit.max_description_seconds`. Demos are optional and must stay under `call.offer_fit.max_demo_minutes`; show the fewest screens needed to test fit. Avoid setup flows, settings pages, and product tours.

After the offer, ask the configured fit-check question. Make it acceptable to say no.

## Close Ask

Only ask for a next step, paid pilot, or purchase when all are true:

- `call.mode` is `pre_pmf_sales`.
- `call.close_ask.mode` is not `none`.
- Demand-fit is at least inferred.
- The participant says the offer conceptually fits, or asks for the next step.

Use `call.close_ask.ask_text` when present. If they say no or hesitate, do not pressure them. Ask what would need to be true for this to be worth a next step and record the answer.

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
- Participant asks for follow-up, access, or introduction.
- Participant accepts or negotiates the configured close ask.

Weak evidence:

- Hypothetical interest.
- No recent example.
- No workaround.
- No consequence.
- Problem belongs to someone else.
- Likes the offer only after the offer fit check.
- Refuses any next step after saying the offer sounds good.
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

Call mode:
Participant signal use:

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
Introduced: yes/no
Reaction:
Objections:
Next-step signal:

## Close Ask
Mode:
Asked: yes/no
Response:
Outcome:

## Hypotheses Updated

## Recommendation
Continue / narrow ICP / revise problem / stop
```

Also produce a cleaned `transcript.md` or interview notes appendix and a small `meta.json` when possible.
