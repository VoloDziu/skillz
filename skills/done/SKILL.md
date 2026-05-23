---
name: done
description: Close out the current in-progress or review issue, stage all changes, and commit with the issue path as the message. Use when the user says "done", "ship it", "commit", "close the issue", "mark as done", "wrap up", or "I'm done".
---

# Done

Close the current issue, commit all staged changes, and use the issue path as the commit message.

The issue tracker structure is in the `## Agent skills` block in CLAUDE.md.

## Steps

### 1. Find the current issue

Scan `.scratch/*/issues/*.md` for an issue with `Status: in-progress` or `Status: review`. There should be at most one.

- If none found: tell the user there's no active issue and stop.
- If found: proceed.

### 2. Mark it done

Change the `Status:` line to `done`.

### 3. Build the commit message

Derive it from the issue file path:

```
<feature-slug>/<NN>-<issue-slug>
```

Example: `.scratch/page-headers-footers/issues/01-schema.md` → `page-headers-footers/01-schema`

### 4. Stage and commit

Stage all changes:

```bash
git add -A
```

Commit with the derived message:

```bash
git commit -m "<feature-slug>/<NN>-<issue-slug>"
```

Report the commit hash and message to the user.
