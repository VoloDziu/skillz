---
name: done
description: Close out the current in-progress or review PRD, stage all changes, and commit with the PRD slug as the message. Use when the user says "done", "ship it", "commit", "close the PRD", "mark as done", "wrap up", or "I'm done".
---

# Done

Close the current PRD, commit all staged changes, and use the PRD slug as the commit message.

The PRD tracker structure is in the `## Agent skills` block in CLAUDE.md.

## Steps

### 1. Find the current PRD

Scan `.scratch/PRD/*.md` for a PRD with `Status: in review`. There should be at most one.

- If none found: tell the user there's no active PRD and stop.
- If found: proceed.

### 2. Mark it done

Change the `Status:` line to `done`.

### 3. Build the commit message

Derive it from the PRD filename:

```
<prd-slug>
```

Example: `.scratch/PRD/page-headers-footers.md` → `page-headers-footers`

### 4. Stage and commit

Stage all changes:

```bash
git add -A
```

Commit with the derived message:

```bash
git commit -m "<prd-slug>"
```

Report the commit hash and message to the user.
