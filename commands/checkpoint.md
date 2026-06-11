Create an intermediate checkpoint for the current session. This saves progress so the user can safely run `/compact` without losing context.

Follow these steps exactly.

## 1. Determine the project
- Check `vault/active-context.md` for the active project.
- If unclear, ask the user.

## 2. Create or update the session log
Check whether a session log for today exists: `vault/session-logs/{project-name}-{YYYY-MM-DD}-*.md`.

**If it exists**, append a checkpoint section:
```markdown

---
## Checkpoint: {HH:MM}

### Done Since Last Checkpoint
<!-- What was accomplished since session start or the last checkpoint -->

### Current State
<!-- What is being worked on right now, any partial progress -->

### Key Context to Preserve
<!-- Details that would be lost on /compact: variable/function names, the
     approach being taken, error messages, specific file paths -->
```

**If it does not exist**, create `vault/session-logs/{project-name}-{YYYY-MM-DD}-{HH-MM}.md` from `vault/projects/_templates/session-log.md` (substitute `{project}` and `{YYYY-MM-DD HH:MM}`), then fill in the sections above. Also add a `## Recent Sessions` entry in the hub `vault/projects/{project-name}/{project-name}.md` (keep last 5).

## 3. Quick-update project files
If any bugs were found/fixed or decisions made, update the relevant files in `vault/projects/{project-name}/`:
- `{project-name}-bug-reports.md`
- `{project-name}-decisions.md`
- `{project-name}-todo.md`

Append/update only — do not rewrite whole files.

## 4. Update active context
Update `vault/active-context.md`:
- Set "Current Focus" to what is being worked on right now.
- Note this is a checkpoint; the session is still active.
- Write every vault path as a wikilink, never plain text.

## 5. Confirm to the user
Output:
```
## Checkpoint Saved: {project-name}
- Log: [[session-logs/{filename}]]
- You can now safely run /compact
- Use /start-session {project-name} to restore context after compact, or just keep working
```

**Important:** Capture enough specific detail (file paths, function names, error messages, the approach being taken) that context can be fully restored. Generic summaries are useless after `/compact`.
