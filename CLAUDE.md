# Claude Code Workspace

> Copy this file to the ROOT of your workspace (the folder that holds your project
> subfolders and the `vault/`). Claude Code loads it automatically every session.

## Obsidian Vault System
This workspace uses an Obsidian vault at `vault/` for persistent project knowledge
across sessions. The vault is the long-term memory; Claude's chat context is not.

### Vault Structure
```
vault/
├── active-context.md              # Current session state, open questions
├── index.md                       # MOC (Map of Content) — central navigation hub
├── session-logs/                  # {project}-{YYYY-MM-DD}-{HH-MM}.md
└── projects/
    ├── _templates/                # Templates for new project pages
    │   ├── overview.md
    │   ├── architecture.md
    │   ├── bug-reports.md
    │   ├── feature-spec.md
    │   ├── decisions.md
    │   ├── todo.md
    │   ├── session-log.md
    │   └── CLAUDE.md
    └── {project-name}/            # One folder per project
        ├── {project-name}.md                  # Hub: what/why/tech + Project Pages + Recent Sessions + Related
        ├── {project-name}-architecture.md     # Components, data flow, infra
        ├── {project-name}-bug-reports.md      # Open and fixed bugs
        ├── {project-name}-feature-spec.md     # Features: planned / in-progress / done
        ├── {project-name}-decisions.md        # Technical/architectural decision log
        └── {project-name}-todo.md             # Task list by priority
```

Filenames in project folders carry the `{project-name}` prefix so each node in the
Obsidian Graph view shows a unique label (e.g. `gym`, `gym-architecture`, not just
`overview`, `architecture`).

### Session Commands
- `/start-session {project}` — Load project context + recent session logs. Creates project pages from templates on first run.
- `/end-session` — Write the session summary; update all project files (bugs, features, decisions, todos) and active-context.
- `/checkpoint` — Save intermediate progress with specific detail so `/compact` is safe.

### Wikilink Conventions (CRITICAL — keeps the Obsidian graph connected)
Every vault path reference MUST be written as a wikilink, never plain text.

**Always apply:**
- `vault/projects/{name}/{name}.md` (hub)        → `[[projects/{name}/{name}]]`
- `vault/projects/{name}/{name}-{type}.md`       → `[[projects/{name}/{name}-{type}]]`
- `vault/session-logs/{file}.md`                 → `[[session-logs/{file}]]`
- `vault/active-context.md`                       → `[[active-context]]`
- `vault/index.md`                                → `[[index]]`

**Do NOT convert:** absolute filesystem repo paths, code references, function names, URLs.

### CLAUDE.md Cascade
```
CLAUDE.md (this file)            ← vault system rules, always loaded
└── {project-name}/CLAUDE.md     ← project-specific vault pointers, loaded when cd'd into the project
```
`/start-session` auto-creates the project CLAUDE.md from the template if missing.

### Conventions
- Each subdirectory in this workspace is a project.
- Session logs: `{project}-{YYYY-MM-DD}-{HH-MM}.md`.
- Project files are append-only during updates — don't rewrite, just add/modify the relevant section.
- `active-context.md` always reflects the current/last session state.
