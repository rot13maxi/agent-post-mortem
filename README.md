# agent-post-mortem

An Amazon-style Correction of Errors (COE) post-mortem skill for Claude Code dev workflows.

## What this is

A structured Claude Code skill that runs a full COE post-mortem whenever something goes wrong in your development process. It interviews you about the incident, walks through the five whys to find the root cause, writes a structured post-mortem document, and offers to implement the fixes.

## Why it exists

"Be more careful next time" is not a root cause. This skill forces the process to find the systemic gap that made the failure possible — and then close it with concrete, typed action items that make the same failure impossible or immediately detectable the next time.

## Usage

Invoke as `/post-mortem` or `/coe` with an optional description:

```
/post-mortem intake form broke after merging 3 PRs
```

```
/coe
```

No description needed — the skill opens with "What went wrong?" if you don't provide one.

## The four phases

1. **Interview** — Understand what broke, how it was discovered, and what the resolution was. The skill asks clarifying questions until the picture is complete.

2. **Five whys** — Walk through the root cause analysis together. Human error is never the root cause; the skill keeps drilling until it finds the systemic gap that made the error possible.

3. **Write the document** — Saves a structured COE to `docs/postmortems/YYYY-MM-DD-<slug>.md` with Summary, Timeline, Resolution, Five Whys, Lessons Learned, and Action Items.

4. **Implement action items** — After writing the doc, the skill presents the action items table and asks which to implement now. It handles each type appropriately.

## Action item types

Every action item is tagged with the type of fix it requires:

| Tag | What it means |
|-----|---------------|
| `[CLAUDE.md]` | Add a constraint, rule, or mandatory checklist item to CLAUDE.md |
| `[SKILL]` | Create or improve a Claude Code skill |
| `[HOOK]` | Add a Claude Code hook that fires on a tool event |
| `[LINT]` | Add a lint rule, TypeScript check, or static analysis assertion |
| `[TEST]` | Add unit, integration, E2E, or browser tests |
| `[CI]` | Add a gate, step, or check to the CI pipeline |
| `[PROCESS]` | Change a human workflow or review practice |

## Installation

```bash
# Clone into your Claude skills directory
git clone https://github.com/rot13maxi/agent-post-mortem ~/.claude/skills/post-mortem
```

Claude Code will automatically detect the skill. Invoke it with `/post-mortem` or `/coe`.

## Example

See [`examples/2026-03-18-uat-gate-no-live-environment.md`](examples/2026-03-18-uat-gate-no-live-environment.md) for a real COE produced by this skill — a P1 incident where a feature's UI shell shipped without the core system behavior implemented.
