# Obsidian + Claude Code: Session Management System

Persistent project memory for [Claude Code](https://claude.com/claude-code), backed by an
[Obsidian](https://obsidian.md) vault. Three slash commands — `/start-session`,
`/checkpoint`, `/end-session` — capture your working context into linked Markdown notes
so nothing is lost between sessions or across `/compact`.

> **Why.** Claude Code's chat context is volatile: it shrinks on `/compact` and resets
> every new session. This system moves the durable knowledge (what the project is, open
> bugs, decisions, TODOs, what happened last session) out of the chat and into a vault
> Claude re-reads on demand. The result: you can close the terminal mid-task and pick up
> next week with full context.

*(Гайд нижче англійською для універсальності; коментуй issue українською — без проблем.)*

---

## What you get

| Command | When | What it does |
|---|---|---|
| `/start-session {project}` | start of work, or after `/compact` | Loads the project's vault pages + last 3 session logs, scans git, prints a brief, creates the project pages on first run. |
| `/checkpoint` | before `/compact` | Dumps the *specific* state (file paths, function names, errors, current approach) into today's session log so `/compact` is safe. |
| `/end-session` | end of work | Writes a full session log and updates every project file (bugs, features, decisions, todos, architecture) + active-context. |

Everything lives as linked Markdown. Open the vault in Obsidian and the
[Graph view](https://help.obsidian.md/Plugins/Graph+view) shows projects, sessions,
decisions and bugs as a connected web.

---

## Repository layout

```
.
├── CLAUDE.md                 # Root rules — copy to your workspace root
├── commands/                 # The 3 slash commands
│   ├── start-session.md
│   ├── checkpoint.md
│   └── end-session.md
└── vault-template/           # Empty vault skeleton — copy to your workspace
    ├── active-context.md
    ├── index.md
    ├── session-logs/
    └── projects/_templates/  # overview, architecture, bug-reports,
                              # feature-spec, decisions, todo, session-log, CLAUDE.md
```

The repo ships an **empty skeleton only** — no real project data. You populate it as you work.

---

## Prerequisites

1. **Claude Code** — install and sign in: <https://docs.claude.com/en/docs/claude-code/overview>
2. **Obsidian** — free desktop app for reading/browsing the vault (Claude writes the files; Obsidian is how *you* see them).

### Install Obsidian
- **macOS:** `brew install --cask obsidian` — or download from <https://obsidian.md/download>
- **Windows:** `winget install Obsidian.Obsidian` — or the installer from the site
- **Linux:** AppImage / Flatpak (`flatpak install flathub md.obsidian.Obsidian`) from the site

Obsidian is optional for the mechanics (Claude reads/writes plain Markdown either way),
but it's the whole point of the linked-notes layout — install it.

---

## Setup (10 minutes)

Assume your Claude Code workspace is one folder that holds all your project subfolders.
Call it `<workspace>` (e.g. `~/Documents/Claude`).

### 1. Drop the system into your workspace
```bash
git clone https://github.com/<you>/obsidian-claude-sessions.git /tmp/ocs

# Root rules (loaded by Claude Code every session)
cp /tmp/ocs/CLAUDE.md <workspace>/CLAUDE.md

# The vault
cp -R /tmp/ocs/vault-template <workspace>/vault
```

### 2. Install the slash commands
Commands live in `~/.claude/commands/` (global) and/or `<workspace>/.claude/commands/` (project-scoped). Global is simplest:
```bash
mkdir -p ~/.claude/commands
cp /tmp/ocs/commands/*.md ~/.claude/commands/
```

### 3. Point the commands at your workspace
The command files refer to `<workspace>` as a placeholder. Replace it with your real
absolute path so `pwd`-detection and `git -C` work:
```bash
# macOS / Linux — adjust the path on the right
sed -i '' 's#<workspace>#/Users/you/Documents/Claude#g' ~/.claude/commands/*.md   # macOS
# sed -i  's#<workspace>#/home/you/Claude#g'              ~/.claude/commands/*.md   # Linux
```

### 4. Open the vault in Obsidian
`Open` → `Open folder as vault` → select `<workspace>/vault`. Enable **Graph view** in the left ribbon.

### 5. Verify
```bash
cd <workspace>
claude
/start-session demo
```
You should get a "Session Started: demo" brief, and `vault/projects/demo/` should now contain `demo.md`, `demo-architecture.md`, … with the project name filled in.

---

## Daily workflow

```text
claude
/start-session gym          # load context (or just /start-session inside the gym/ folder)

… work …

/checkpoint                 # before compacting — saves the specifics
/compact                    # shrink Claude's chat context safely

… keep working …

/end-session                # write the log, update all project files
```

### Starting a brand-new project
```bash
mkdir <workspace>/new-project && cd <workspace>/new-project
claude
/start-session              # auto-creates vault/projects/new-project/ from templates + CLAUDE.md
```

---

## How context is preserved correctly

This is the part people get wrong. A few rules make the vault stay coherent and the
Obsidian graph stay connected:

1. **Unique filenames per project.** Files are prefixed: `gym.md`, `gym-architecture.md`,
   not `overview.md`, `architecture.md`. Otherwise every project shows identical
   "overview" / "architecture" nodes in the graph and they collide.
2. **Everything is a wikilink.** Any reference to a vault file is written `[[…]]`, never
   as a plain path. That's what builds the graph. Absolute repo paths, code symbols and
   URLs stay literal.
3. **One hub per project.** `{project}.md` is the hub: overview + Project Pages +
   Recent Sessions (last 5) + Related Projects. Sessions and pages link back to it.
4. **Append, never rewrite.** `/end-session` adds to bug/decision/todo files; it doesn't
   regenerate them. History accumulates.
5. **Specifics, not summaries, at `/checkpoint`.** "Refactored auth" is useless after
   `/compact`. "Changed `validateToken()` in `src/auth/token.ts:42` to check `exp`
   before `iat`; tests in `token.test.ts` still red on the clock-skew case" is restorable.
6. **`active-context.md` is the cross-session scratchpad** — always reflects the last state.

> **Tip — pair it with Claude Code memory.** Claude Code also has its own file-based
> memory. The vault is for *project* knowledge; memory is for *durable preferences and
> facts about you*. They complement each other.

---

## Customizing

- **Different workspace path?** Re-run the `sed` in step 3.
- **More project page types?** Add a template to `vault/projects/_templates/` and extend
  the `/start-session` step-2 copy list.
- **Project-scoped commands** (so a teammate cloning the repo gets them): put copies in
  `<workspace>/.claude/commands/` and commit that folder.

---

## Scaling up to a full "second brain"

This is the **lightweight** session-tracking layer. If you outgrow it — you want
PARA organization, ADR-style decision records, schema validation, drift/lint reports, or
CI that checks the vault — that's a heavier "second-brain" system built on the same
Markdown-vault idea. Start here; graduate when the session log isn't enough.

---

## License

MIT — see [LICENSE](LICENSE).
