End the current working session and save all context.

Follow these steps exactly.

## 1. Determine the project
- Check `vault/active-context.md` for the active project.
- If unclear, ask the user.

## 2. Normalize, then write the session log
If a session log for today already exists (from `/checkpoint`), append a final section to it. Otherwise create `vault/session-logs/{project-name}-{YYYY-MM-DD}-{HH-MM}.md` from `vault/projects/_templates/session-log.md`.

Before saving, **normalize the log**: replace any plain-text vault path with a wikilink (e.g. `vault/projects/x/x.md` → `[[projects/x/x]]`, `vault/session-logs/y.md` → `[[session-logs/y]]`). Do NOT convert absolute filesystem repo paths, function names, or URLs.

Fill these sections:
```markdown
## Summary
<!-- 2-3 sentence high-level summary of the session -->

## What Was Done
<!-- Bulleted concrete accomplishments -->

## Files Changed
<!-- Repo files modified/created/deleted (absolute paths) with a brief note -->

## Bugs Found / Fixed
<!-- Any bugs discovered or resolved -->

## Decisions Made
<!-- Technical/architectural decisions and their reasoning -->

## Open Questions
<!-- Unresolved items -->

## Next Steps
<!-- What to do next session -->
```

## 3. Update project files
Read and update each relevant file in `vault/projects/{project-name}/` (append/update only — never rewrite a whole file):
- `{project-name}-bug-reports.md` — add new bugs, move fixed ones to Fixed. New entry → append `| session: [[session-logs/{this-file}]]`.
- `{project-name}-feature-spec.md` — update statuses, add new features. New FEAT → append `| session: [[session-logs/{this-file}]]`.
- `{project-name}-decisions.md` — add new decisions (DEC-NNN). When adding a DEC, ask the user: "Add `**Related:** [[{project-name}-architecture#...]], [[{project-name}-feature-spec#FEAT-...]]` for traceability?"
- `{project-name}-todo.md` — add new tasks, check off completed, reprioritize.
- `{project-name}-architecture.md` — update if architecture changed.
- `{project-name}.md` (hub) — update status if project state changed.

Create any missing file from `vault/projects/_templates/` (rename + prefix wikilinks + replace `{Project Name}` per `/start-session` rules).

## 4. Update active context
`active-context.md` is a snapshot of the current/last state, NEVER an append-log. Update in place:
- Add a link to the new session log under "Recent Sessions" (keep last 5).
- Update "Open Questions" with unresolved items.
- **Overwrite** "Current Focus" with the next steps. Do NOT demote the old focus into a `## Previous Focus` block — that history already lives in the session log. Keep exactly one Current Focus and zero Previous Focus sections.
- Write every vault path as a wikilink immediately, never plain text.

## 5. Cross-link prompt
If the session referenced another vault project (e.g. "reused a pattern from project X"), ask the user: "Add a cross-link under `## Related Projects` in the hub?"

## 6. Confirm to the user
Output:
```
## Session Ended: {project-name}
- Session log: [[session-logs/{filename}]]
- Updated: {which project files changed}
- Next steps: {brief list}
```
