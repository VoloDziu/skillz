---
name: engage
description: Pick up and implement a PRD-tracker item end-to-end. Use when the user explicitly invokes /engage, asks to pick up the next PRD, asks what PRD/backlog item to work on next, or says to resume PRD-tracked work. Do not use for generic coding requests, one-off feature requests, or casual "let's build X" prompts unless the user connects the work to the PRD tracker.
---

# Engage

Work a PRD-tracker item end-to-end: pick it up, plan the implementation, implement it, and close it out.

The PRD tracker structure is in `AGENTS.md` — run `/setup` if that file doesn't exist.

---

## Step 1: Orient

Before picking up a PRD, check the current state of the tracker:

1. Scan all `.scratch/PRD/*.md` files
2. Look for PRDs with `Status: in review`

- **One in-review PRD found** → surface it: tell the user which feature is awaiting review and say `/done` closes and commits it
- **Multiple in-review PRDs found** → tell the user the tracker is ambiguous and stop
- **None** → go to [Pick Next PRD](#pick-next-prd)

---

## Pick Next PRD

Find PRDs with `Status: todo`.

- **No todo PRDs**: tell the user and stop. Suggest running `/grill-me` to create one.
- **One candidate**: confirm with the user, then go to [Gather Context](#gather-context).
- **Multiple candidates**: show a compact list — slug + one-liner from the Problem Statement — and ask the user to choose. Once chosen, go to [Gather Context](#gather-context).

---

## Gather Context

Collect everything needed to plan the work. Do these in parallel:

1. Read the full PRD file
2. Read `AGENTS.md`, including shared vocabulary and decision-record instructions
3. Read any `docs/adr/*.md` relevant to this area
4. Explore the codebase: find files related to what the PRD touches

Synthesize what you found — don't just list sources. Explain:
- What the feature is trying to accomplish
- Where in the codebase this work will land
- Any constraints from `AGENTS.md` vocabulary or ADRs that shape the approach

Ask the user to fill in any gaps before drafting a plan.

---

## Plan

Draft a detailed implementation plan as a markdown checklist. Each item should name the area of change, describe what changes, and briefly say why.

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

Once explicitly confirmed, proceed to [Implement](#implement). No status change yet — the PRD stays `todo` during planning.

Do not append the plan to the PRD file — keep it in the conversation only. The PRD's Solution and Implementation Decisions sections are the durable record; the implementation plan is a working artifact for this session.

---

## Implement

Work through the implementation plan top to bottom. If something unexpected comes up mid-implementation that changes the approach, stop and discuss with the user before continuing.

When implementation is complete (all plan steps done, or the user signals it):
1. Change `Status: todo` → `Status: in review` in the PRD file
2. Tell the user: "Implementation complete — PRD is now in review. Run `/done` when you're happy with it."

---

## Status Transitions

| Trigger | From | To |
|---|---|---|
| Implementation complete | `todo` | `in review` |
| `/done` run | `in review` | `done` |
