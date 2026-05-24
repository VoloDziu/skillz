---
name: setup
description: Writes a fixed `## Agent skills` block into CLAUDE.md/AGENTS.md so that `grill-with-docs` and `engage` know how this repo is structured. Run once per repo before using those skills.
disable-model-invocation: true
---

# Setup Skills

Write a fixed `## Agent skills` block into CLAUDE.md or AGENTS.md.

## Process

### 1. Explore

Check `CLAUDE.md` at the root — does it exist? Is there already an `## Agent skills` section?

### 2. Write

Use `CLAUDE.md`. Create it if it doesn't exist.

If an `## Agent skills` block already exists, update it in-place rather than appending a duplicate.

Write this block:

```markdown
## Agent skills

### PRD tracker

PRDs live as files under `.scratch/PRD/`:

- PRD: `.scratch/PRD/<prd-slug>.md`
- Track status with a `Status:` line at the very top of the file: `todo`, `in review`, or `done`

### Domain docs

Read `CONTEXT.md` at the repo root before exploring the codebase. Read any ADRs in `docs/adr/` that touch the area you're working in. If these files don't exist, proceed silently. Use the vocabulary from `CONTEXT.md` exactly; flag any contradiction with an existing ADR rather than silently overriding it.
```

### 3. Done

Tell the user setup is complete. `grill-with-docs` and `engage` will now read from this block.
