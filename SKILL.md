---
name: post-mortem
description: Run an Amazon-style Correction of Errors (COE) post-mortem on a development process failure. Produces a structured document with summary, timeline, five-whys root cause analysis, lessons learned, and typed action items — then offers to implement the fixes. Use this whenever something goes wrong in the dev process: a feature passes tests but doesn't actually work, functionality gets clobbered by a merge, an implementation is declared done but breaks on basic cases, a regression slips through, QA finds something that should have been caught earlier, or any incident that reveals a gap in the process. Invoke as /post-mortem or /coe with an optional brief description: e.g., `/post-mortem intake form broke after merging 3 PRs` or just `/coe`. Don't wait for the user to ask — if something went wrong, suggest running /post-mortem proactively.
---

# Post-Mortem (COE) Skill

You're running an Amazon-style Correction of Errors process. The goal isn't paperwork — it's finding the *real* root cause and creating *real* fixes so this class of failure doesn't happen again. Be a collaborative thought partner, not a doc-filling machine.

## Phase 1: Understand the incident

If the user passed a description as args, start from that. Otherwise open with: "What went wrong?" and listen before asking follow-up questions.

Once you have an initial description, fill in any gaps you need:

- **What was expected vs. what actually happened?**
- **When and how was it discovered?** (during dev, code review, QA, production, user report)
- **What was the resolution?** How was it fixed?
- **Rough timeline?** When did the broken thing get written? When noticed? When fixed?

Don't rush this. If answers are vague, ask clarifying questions. Precision here pays off in the root cause analysis.

## Phase 2: Five Whys

This is the heart of the COE. Walk through the five whys with the user.

Start with the observable failure: "Why did [X] happen?"

Each answer becomes the next "why." Keep drilling until you reach a systemic root cause — something structural, not just "someone forgot." Human error is never a root cause; it's a symptom. The real question is: what made this error possible or likely in the first place?

**Signs you've reached a real root cause:**
- It's systemic — a different person doing the same thing would likely hit the same failure
- Fixing it prevents a whole *class* of similar failures
- It points to a missing guardrail, process gap, or absent feedback loop

**Signs you're still at a symptom:**
- It can be restated as "someone didn't [X]"
- The fix is "be more careful next time"
- It doesn't explain why the error was *easy to make*

Push back gently if the user lands somewhere shallow. "That's true, but what made it *possible* for that to happen?" Aim for 5 whys, but stop at genuine bedrock — sometimes that's 4, sometimes 6.

## Phase 3: Write the document

Save the COE to `docs/postmortems/YYYY-MM-DD-<slug>.md` where slug is a 3-5 word kebab-case summary (e.g., `2026-03-18-feature-marked-done-but-broken.md`). Create the `docs/postmortems/` directory if it doesn't exist.

Use this exact structure:

```markdown
# COE: [Incident Title]

**Date:** YYYY-MM-DD
**Severity:** [P0 = production broken / P1 = feature unusable / P2 = degraded/caught pre-ship]
**Status:** Resolved

## Summary

One paragraph. What broke, how it was found, what fixed it, and what's changing to prevent recurrence. Written for someone with 30 seconds.

## Timeline

| Time | Event |
|------|-------|
| T+0  | [Earliest relevant event — when the broken code was written or merged] |
| ...  | ... |
| T+N  | Incident fully resolved |

Use relative times if absolutes aren't known (T+0, T+2h, T+1d).

## Resolution

What was done to fix the immediate problem. Specific: commit hash, PR number, or exact change if known.

## Five Whys

**Why did [observable failure] happen?**
Because [answer 1].

**Why did [answer 1] happen?**
Because [answer 2].

...continue until root cause...

**Root Cause:** [One sentence. The underlying systemic issue.]

## Lessons Learned

- What assumptions turned out to be wrong?
- What signals were missed that could have caught this earlier?
- What made this failure mode possible or easy?

## Action Items

| # | Action | Type | Owner | Priority |
|---|--------|------|-------|----------|
| 1 | [Specific, concrete action] | [CLAUDE.md / SKILL / HOOK / LINT / TEST / CI / PROCESS] | [Claude / Human] | [P0 / P1 / P2] |
```

## Action item type tags

- `[CLAUDE.md]` — Add a constraint, rule, or mandatory checklist item to CLAUDE.md
- `[SKILL]` — Create or improve a Claude Code skill (a structured workflow that prevents this)
- `[HOOK]` — Add a Claude Code hook that fires on a tool event (e.g., block Write if condition not met)
- `[LINT]` — Add a lint rule, TypeScript check, or static analysis assertion
- `[TEST]` — Add unit, integration, E2E, browser, or generative tests
- `[CI]` — Add a gate, step, or check to the CI pipeline
- `[PROCESS]` — Change a human workflow or review practice

Good action items are specific. "Improve testing" is not an action item. "Add an E2E test covering the intake form submit path with an empty required field" is. Each action item should, if completed, make this exact failure *impossible* or *immediately detectable*.

## Phase 4: Offer to implement

After writing the doc, present the action items table and ask: "Which of these would you like to implement now?"

For "now" items:
- `[CLAUDE.md]` → Edit the CLAUDE.md directly
- `[SKILL]` → Use the `/skill-creator` skill to design and build it
- `[HOOK]` → Use `/update-config` to add the hook to settings.json
- `[LINT]` → Add the rule to the ESLint/TypeScript config
- `[TEST]` → Write and place the test in the right location
- `[CI]` → Add the step to the CI config

For deferred items — they're in the document. That's enough for now.

## Tone

Use "we" language. The process failed, not a person. This is a retrospective, not a blame session.

The best COEs produce action items so targeted that once done, the failure *cannot recur* or *will be immediately caught*. Aim for that bar. A COE that concludes "we should be more careful" is worse than nothing.
