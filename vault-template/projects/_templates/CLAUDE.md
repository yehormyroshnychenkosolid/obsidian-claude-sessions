# {Project Name}

## Vault Context
This project's knowledge base lives in the Obsidian vault. Before starting work, read:
- `vault/projects/{project-name}/{project-name}.md` — project hub: overview, status, pages, recent sessions
- `vault/projects/{project-name}/{project-name}-architecture.md` — system architecture
- `vault/projects/{project-name}/{project-name}-bug-reports.md` — known bugs
- `vault/projects/{project-name}/{project-name}-todo.md` — current tasks

Use `/start-session {project-name}` for a full context load including session history.

## Session Workflow
- `/checkpoint` before `/compact` to preserve context safely
- `/end-session` when done — updates all vault project files
