---
name: setup
description: Writes a fixed `## Agent skills` block into CLAUDE.md/AGENTS.md so that `to-issues`, `to-prd`, and `grill-with-docs` know how this repo is structured. Run once per repo before using those skills.
disable-model-invocation: true
---

# Setup Skills

Write a fixed `## Agent skills` block into CLAUDE.md or AGENTS.md.

## Process

### 1. Explore

Check `AGENTS.md` and `CLAUDE.md` at the root — which exists? Is there already an `## Agent skills` section?

### 2. Write

Pick the file:

- If `CLAUDE.md` exists, edit it.
- Else if `AGENTS.md` exists, edit it.
- If neither exists, ask the user which to create.

Never create both. If an `## Agent skills` block already exists, update it in-place rather than appending a duplicate.

Write this block:

```markdown
## Agent skills

### Issue tracker

Issues live as files under `.scratch/`:

- Feature directory: `.scratch/<feature-slug>/`
- PRD: `.scratch/<feature-slug>/PRD.md`
- Issues: `.scratch/<feature-slug>/issues/<NN>-<slug>.md`, numbered from `01`
- Track status with a `Status:` line near the top: `todo`, `in-progress`, `done`, or `wontfix`
- Append comments under a `## Comments` heading at the bottom

### Domain docs

Read `CONTEXT.md` at the repo root before exploring the codebase. Read any ADRs in `docs/adr/` that touch the area you're working in. If these files don't exist, proceed silently. Use the vocabulary from `CONTEXT.md` exactly; flag any contradiction with an existing ADR rather than silently overriding it.
```

### 3. Done

Tell the user setup is complete. `to-issues`, `to-prd`, and `grill-with-docs` will now read from this block.
