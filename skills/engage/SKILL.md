---
name: engage
description: Start or resume an issue from the project issue tracker. Use this whenever you want to pick up work, plan an issue, or resume an in-progress ticket. Triggers on: "engage", "start an issue", "next issue", "what should I work on", "pick up a ticket", "let's work on something", "resume", "back to work", "implementation is done", "ready for review".
---

# Engage

Work an issue from the project issue tracker end-to-end: pick it up, plan it, implement it, and close it out.

The issue tracker structure is in the `## Agent skills` block in CLAUDE.md — run `/setup` if that block doesn't exist.

---

## Step 1: Orient

Before picking a new issue, check the current state of the tracker:

1. Scan all `.scratch/*/issues/*.md` files
2. Look for any issue with `Status: in-progress` — there should be at most one
3. Look for any issue with `Status: review`

- **In-progress issue found** → go to [Resume In-Progress](#resume-in-progress)
- **Review issue found** → surface it: tell the user which issue is awaiting review and ask if they're ready to close it
- **Neither** → go to [Pick Next Issue](#pick-next-issue)

---

## Pick Next Issue

Find unblocked issues: `Status: todo` where every item in "Blocked by" is `Status: done` (or "None — can start immediately").

- **No unblocked issues**: tell the user and stop. Suggest checking if blockers are done or creating new issues with `/to-issues`.
- **One candidate**: confirm with the user, then go to [Gather Context](#gather-context).
- **Multiple candidates**: show a compact list — title + one-liner from "What to build" — and ask the user to choose. Once chosen, go to [Gather Context](#gather-context). Offer to expand any entry if the user wants more detail before deciding.

---

## Gather Context

Collect everything needed to plan the work. Do these in parallel:

1. Read the full issue file
2. Read `.scratch/<feature>/PRD.md` if it exists
3. Read `CONTEXT.md` at the repo root if it exists
4. Read any `docs/adr/*.md` relevant to this area
5. Explore the codebase: find files related to what the issue touches

Synthesize what you found — don't just list sources. Explain:
- What the feature is trying to accomplish
- What this specific issue asks for
- Where in the codebase this work will land
- Any constraints from ADRs or CONTEXT.md that shape the approach

Ask the user to fill in any gaps before drafting a plan.

---

## Plan

Draft a detailed implementation plan as a markdown checklist. Each item should name the file, describe the change, and briefly say why.

```markdown
## Implementation Plan

- [ ] Step 1: Add `X` field to `schema.ts` — needed for Y
- [ ] Step 2: Update `service.ts` to handle X
  - [ ] Sub-step: validate X is present before calling Z
- [ ] Step 3: Wire up in `api.ts`
- [ ] Step 4: Add test for the new behavior in `service.test.ts`
```

Present the plan and iterate until the user approves it. Ask:
- Does the approach make sense?
- Are there steps missing or in the wrong order?
- Any open questions or concerns?

The plan is the most valuable output here — take the time to get it right.

**Hard stop: do not write any files, change any status, or begin implementation until the user explicitly confirms the plan** (yes / go / approve / looks good). Silence, a follow-up question, or a minor tweak is not confirmation.

Once explicitly confirmed:
1. Change `Status: todo` → `Status: in-progress`
2. Proceed to [Implement](#implement)

Do not append the plan to the issue file — keep it in the conversation only. The issue's acceptance criteria are the durable record of done; the implementation plan is a working artifact for this session.

---

## Implement

Work through the implementation plan top to bottom. As each acceptance criterion in the issue file becomes satisfied, check it off immediately:

```
- [x] Criterion that is now met
```

If something unexpected comes up mid-implementation that changes the approach, stop and discuss with the user before continuing.

When all acceptance criteria are checked off (or the user signals implementation is complete):
1. Change `Status: in-progress` → `Status: review`
2. Tell the user: "Implementation complete — issue is now in review. Let me know once you've looked it over."

---

## Resume In-Progress

An issue is already in-progress. Reconstruct where things stand before continuing:

1. Read the issue file — review the acceptance criteria checklist and any notes
2. Run `git diff HEAD` and `git status` to see what has actually changed
3. Cross-reference the diff against the acceptance criteria:
   - Criteria clearly satisfied by the diff → likely complete, note them
   - Criteria untouched → still pending

Present a clear status summary to the user:
- Which acceptance criteria appear done (with evidence from the diff)
- Any discrepancies or surprises that need a decision
- What remains

Re-draft the implementation plan from scratch based on what's left, then ask the user to confirm the current state and plan before continuing.

---

## Status Transitions

| Trigger | From | To |
|---|---|---|
| Plan approved | `todo` | `in-progress` |
| Implementation complete | `in-progress` | `review` |

Recognize natural language signals for each transition:
- **Done implementing**: "done", "implementation complete", "ready for review", "finished"
