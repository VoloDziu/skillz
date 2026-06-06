---
name: grill-me
description: Grilling session that challenges a plan against AGENTS.md shared vocabulary, existing code, and ADRs; sharpens terminology; updates AGENTS.md vocabulary and ADRs inline as decisions crystallise; and creates a PRD at the end. Use when the user invokes /grill-me or asks to stress-test a plan against the project's language and documented decisions.
---

Interview me relentlessly about every aspect of this plan until we reach a shared understanding. Walk down each branch of the design tree, resolving dependencies between decisions one-by-one. For each question, provide your recommended answer.

Ask the questions one at a time, waiting for feedback on each question before continuing.

If a question can be answered by exploring the codebase, explore the codebase instead.

## Domain awareness

During codebase exploration, also look for existing documentation:

### File structure

```
/
├── AGENTS.md
├── docs/
│   └── adr/
│       ├── 0001-event-sourced-orders.md
│       └── 0002-postgres-for-write-model.md
└── src/
```

Create files lazily — only when you have something to write. If no `AGENTS.md` exists, tell the user to run `/setup` before writing shared vocabulary. If no `docs/adr/` exists, create it when the first ADR is needed.

When creating or updating domain docs, load only the format reference needed for the artifact:
- `references/adr-format.md` for ADRs
- `references/prd-format.md` for PRDs

## During the session

### Challenge against the glossary

When the user uses a term that conflicts with the existing language in the `## Shared vocabulary` section of `AGENTS.md`, call it out immediately. "Your vocabulary defines 'cancellation' as X, but you seem to mean Y — which is it?"

### Sharpen fuzzy language

When the user uses vague or overloaded terms, propose a precise canonical term. "You're saying 'account' — do you mean the Customer or the User? Those are different things."

### Discuss concrete scenarios

When domain relationships are being discussed, stress-test them with specific scenarios. Invent scenarios that probe edge cases and force the user to be precise about the boundaries between concepts.

### Cross-reference with code

When the user states how something works, check whether the code agrees. If you find a contradiction, surface it: "Your code cancels entire Orders, but you just said partial cancellation is possible — which is right?"

### Update AGENTS.md inline

When a term is resolved, add the exact entry to the `## Shared vocabulary` section of `AGENTS.md` immediately. Tell the user what entry you are adding before writing it. Do not ask for confirmation before writing resolved shared vocabulary.

Do not couple shared vocabulary to implementation details. Only include terms that are meaningful to domain experts.

### Write ADRs sparingly

Only create an ADR when all three are true:

1. **Hard to reverse** — the cost of changing your mind later is meaningful
2. **Surprising without context** — a future reader will wonder "why did they do it this way?"
3. **The result of a real trade-off** — there were genuine alternatives and you picked one for specific reasons

If any of the three is missing, skip the ADR. Use the format in `references/adr-format.md`.

When all three conditions are met, tell the user this decision needs an ADR, summarize the decision briefly, load `references/adr-format.md`, and write the ADR immediately. Do not ask for confirmation before writing ADRs.

## Wrapping up

When the grilling session reaches a natural end (all branches of the design tree resolved, no more open questions, user signals they're done), tell the user:

> "We've covered the ground. This grill session is done; I will now create the PRD."

Then synthesize the entire session into a PRD using `references/prd-format.md`. Explore the repo first if needed to ground implementation decisions in the actual codebase. Do not ask whether a PRD should be created, do not present the draft for confirmation, and do not wait for approval before saving it.

1. Derive a slug from the PRD title (lowercase, hyphenated)
2. Save to `.scratch/PRD/<prd-slug>.md` with `Status: todo` at the very top
