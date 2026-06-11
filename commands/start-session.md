Start a new working session for project: $ARGUMENTS

Follow these steps exactly.

## 1. Identify the project
Determine the project name using this priority:
1. **Explicit argument**: if `$ARGUMENTS` is not empty, use it as the project name.
2. **Current directory**: if the working directory is inside `<workspace>/{something}/`, use `{something}` (run `pwd` to check).
3. **Active context**: check `vault/active-context.md` for the last active project.
4. **Ask the user** if none of the above works.

- Project code directory: `<workspace>/{project-name}/`
- Project vault directory: `vault/projects/{project-name}/`

## 2. First-time project setup
If `vault/projects/{project-name}/` does not exist, create it:

a. Copy these 6 templates from `vault/projects/_templates/` into `vault/projects/{project-name}/` and **rename each** so every node has a unique label in the Obsidian graph:
   - `overview.md`      → `{project-name}.md`        (this is the **hub** file)
   - `architecture.md`  → `{project-name}-architecture.md`
   - `decisions.md`     → `{project-name}-decisions.md`
   - `feature-spec.md`  → `{project-name}-feature-spec.md`
   - `bug-reports.md`   → `{project-name}-bug-reports.md`
   - `todo.md`          → `{project-name}-todo.md`

b. In each renamed file, rewrite the internal wikilinks to the prefixed names (so the graph stays connected):
   - `[[overview]]`     → `[[{project-name}]]`
   - `[[architecture]]` → `[[{project-name}-architecture]]`
   - `[[decisions]]`    → `[[{project-name}-decisions]]`
   - `[[feature-spec]]` → `[[{project-name}-feature-spec]]`
   - `[[bug-reports]]`  → `[[{project-name}-bug-reports]]`
   - `[[todo]]`         → `[[{project-name}-todo]]`

c. Replace every `{Project Name}` placeholder with the real project name.

d. Create `<workspace>/{project-name}/CLAUDE.md` from `vault/projects/_templates/CLAUDE.md` (if missing), replacing `{Project Name}` and `{project-name}`. This enables the CLAUDE.md cascade: root CLAUDE.md (vault rules) → project CLAUDE.md (project pointers).

## 3. Load project context
Read these files from `vault/projects/{project-name}/` (skip any that don't exist yet):
- `{project-name}.md` — hub: overview, tech stack, status, recent sessions
- `{project-name}-architecture.md`
- `{project-name}-bug-reports.md`
- `{project-name}-feature-spec.md`
- `{project-name}-decisions.md`
- `{project-name}-todo.md`

Also read `vault/active-context.md` for cross-session state.

## 4. Load recent session history
- List files in `vault/session-logs/` starting with the project name.
- Read the **last 3 session logs** (most recent first). These carry what was done, open questions, and next steps.

## 5. Quick scan project state
If `<workspace>/{project-name}/` is a git repo:
- `git -C <workspace>/{project-name} log --oneline -10`
- `git -C <workspace>/{project-name} status --short`

(Ignore if not a git repo.)

## 6. Create the session log
Create `vault/session-logs/{project-name}-{YYYY-MM-DD}-{HH-MM}.md` from `vault/projects/_templates/session-log.md`, substituting `{project}` and `{YYYY-MM-DD HH:MM}`. The `**Project:**` field uses `[[../projects/{project-name}/{project-name}]]`.

## 7. Register the session
- In the hub `vault/projects/{project-name}/{project-name}.md`, under `## Recent Sessions`, add a one-line entry at the top linking the new log: `- {YYYY-MM-DD HH:MM} — [[session-logs/{file}]] — (in progress)`. Keep only the last 5 (drop the oldest).
- If the project isn't in `vault/index.md` under Active Projects, add it.

## 8. Present the session brief
Output a concise brief:
```
## Session Started: {project-name}
### Current State
{summary from hub + architecture + active-context}

### Open Bugs
{open items from {project-name}-bug-reports.md}

### TODO
{high-priority items from {project-name}-todo.md}

### Last Session Recap
{key points from the most recent session log: done, open questions, next steps}

### Suggested Focus
{next steps + open bugs + high-priority todos}
```

## 9. Update active context
Update `vault/active-context.md` with the current project name and session start time. Write any vault path as a wikilink, never plain text.

**Important:** Be concise. The goal is to restore context fast, not write an essay.
