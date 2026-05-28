---
name: setup
description: Create or update repo agent instructions. Use only when the user explicitly invokes `/setup` or directly asks to run the setup skill. Replaces AGENTS.md with the fixed Skillz agent rules and makes CLAUDE.md a symlink to it.
---

# Setup Skills

Create the shared repo instruction file.

## Process

### 1. Inspect

Check the repo root:

- Does `AGENTS.md` exist?
- Does `CLAUDE.md` exist?
- Is `CLAUDE.md` already a symlink to `AGENTS.md`?
- Does `AGENTS.md` contain content that must be migrated before replacement?
- Does `CLAUDE.md` contain non-symlink content that must be migrated before replacement?

### 2. Write

Use `AGENTS.md` as the generated source file. Replace the entire file with the content below.

If `AGENTS.md` already exists and contains project instructions that should survive, summarize them for the user and ask where they belong before continuing. Do not silently discard existing `AGENTS.md` content.

If `CLAUDE.md` is a regular file, inspect it before replacing it with a symlink. If it contains project instructions that should survive, summarize them for the user and ask where they belong before continuing. Do not silently discard non-symlink `CLAUDE.md` content.

Write this exact file:

```markdown
## Writing style

Write tight, professional prose. Use full sentences with articles, but no filler and no hedging.

Banned words and phrases: "just", "really", "basically", "essentially", "simply", "actually", "I'll", "let me", "happy to", "great question", "I think", "maybe", "perhaps", "feel free to", "of course", "certainly", "definitely".

State the result first, then the reason if it matters. Code, commands, and error text stay verbatim. Before sending, scan the response for banned words and rewrite if found. Drop this style only for security warnings, destructive-action confirmations, or multi-step sequences where order matters; then resume.

## Code editing rules

Apply these rules when writing or modifying TypeScript or React code:

- Structure: no one-use indirection. Do not create a local alias, wrapper function, extracted type, helper, component, hook, or module-level cache unless it has 2+ call sites, hides real complexity, or names a domain concept used elsewhere. Prefer using the source expression directly for single-use values, including `ref.current`, object properties, props, and derived constants. Inline single-use prop/param types. No nested ternaries, `switch`, or component IIFEs. Use named-param objects for regular functions with 2+ params, early returns, positive 2-branch conditions, and braces on every block.
- React: no `useCallback`/`useMemo` without measured need; no destructuring hook returns or props except tuple hooks; prefer pure functions over components over hooks; keep business logic out of `useEffect`; hoist feature/experiment branching above existing components.
- TypeScript: no `as`, non-null `!`, `eslint-disable`, `@ts-ignore`, `@ts-expect-error`, or default exports.
- Co-location: helpers stay with their only caller; variables use the narrowest scope; extract cohesive UI clusters; pass entities/IDs and derive display values where consumed.
- Safety/testing/tooling: no silent defaults for money or bug-signaling values; fix tests instead of production code unless there is a real bug; keep tests synced with new/changed/moved code; never stage/commit/push unless explicitly asked (`/done` counts).
- General: no comments unless the why is non-obvious; refactor copied code before using it.

## PRD tracker

PRDs live as files under `.scratch/PRD/`:

- PRD: `.scratch/PRD/<prd-slug>.md`
- Track status with a `Status:` line at the very top of the file: `todo`, `in review`, or `done`

## Shared vocabulary

Use this section for canonical domain terms and workflow language. Add entries when terminology is resolved. Keep entries short and meaningful to domain experts.

When the user asks for a change, check whether the change aligns with this vocabulary. If the request uses conflicting or unclear terminology, raise the mismatch before implementing. If the vocabulary is wrong or incomplete, propose the exact amendment and ask for confirmation before updating `AGENTS.md`.

## Decision records

Read any ADRs in `docs/adr/` that touch the area you're working in. If ADRs don't exist, proceed silently. When the user asks for a change, check whether the change aligns with relevant ADRs. If it conflicts with an ADR, raise the mismatch before implementing. If the ADR is outdated or the decision needs to change, propose the exact amendment or replacement and ask for confirmation before updating ADR files.
```

### 3. Link

Make `CLAUDE.md` a relative symlink to `AGENTS.md`:

```bash
ln -s AGENTS.md CLAUDE.md
```

If `CLAUDE.md` already exists as a symlink to `AGENTS.md`, leave it alone.

If `CLAUDE.md` exists as a regular file, only replace it with the symlink after its contents have been migrated into `AGENTS.md`.

### 4. Done

Report that setup is complete, `AGENTS.md` was replaced, and `CLAUDE.md` points to `AGENTS.md`.
