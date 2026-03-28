---
name: import
description: Use this skill to import a Claude Code capability (skill, agent, hook, rule, context) from a GitHub repo into your local .claude/ directory. Handles adaptation strategy selection (verbatim through full rewrite), correct path placement, and provenance tracking via upstream.yaml. Invoke whenever the user wants to import, fetch, grab, or add a capability from an external repo.
---

# /import

## Purpose

Import a capability from an external GitHub repo into your local project. Walks you through choosing how to adapt the upstream content (verbatim copy through full rewrite), places the file in the correct `.claude/` directory, writes a provenance sidecar (`upstream.yaml`), and updates `sources.json`.

## Usage

```
/import <source> <capability-path>
```

Where:
- `<source>` is a short name from `sources.json` OR a full `https://github.com/owner/repo` URL
- `<capability-path>` is the relative path to the capability inside the upstream repo (e.g. `skills/brainstorming/SKILL.md`)

**Examples:**
```
/import superpowers skills/brainstorming/SKILL.md
/import https://github.com/wshobson/agents agents/code-reviewer.md
```

## Flow

### 1. Parse and validate arguments

- If `<source>` is a short name, look it up in `sources.json` to get the URL. If not found, list available names and exit.
- If `<source>` is a `https://` URL, use it directly.
- If `<capability-path>` is missing, print usage and exit.

### 2. Clone the source repo

```bash
TMPDIR_IMPORT=$(mktemp -d /tmp/cull-import-XXXXXX)
git clone --depth 1 <url> "$TMPDIR_IMPORT"
```

Record the HEAD commit hash for provenance:
```bash
COMMIT_HASH=$(git -C "$TMPDIR_IMPORT" rev-parse HEAD)
```

Clean up on exit (success or failure):
```bash
trap "rm -rf '$TMPDIR_IMPORT'" EXIT
```

### 3. Read the upstream capability

Check that `<capability-path>` exists in the cloned repo. If not, report the error and suggest running `/explore <source>` first to browse available capabilities.

Display the upstream file content to the user.

### 4. Determine local target path

Use this mapping to determine where to place the file. Resolve this before asking about adaptation — the user may want to rename, and you need the target path for the collision check.

| Upstream path pattern | Local target |
|---|---|
| `skills/*/SKILL.md` | `.claude/skills/<name>/SKILL.md` |
| `agents/*.md` | `.claude/agents/<name>.md` |
| `hooks/**/*.js` | `.claude/hooks/<name>.js` |
| `rules/<category>/<name>.md` | `.claude/rules/<category>/<name>.md` |
| `rules/<name>.md` (flat) | Ask user for category, then `.claude/rules/<category>/<name>.md` |
| `contexts/*.md` | `.claude/contexts/<name>.md` |

If the path matches none of the patterns, ask the user where to place it.

### 5. Ask for adaptation strategy

Present the five strategies and ask the user to choose one:

| # | Strategy | Description |
|---|---|---|
| 1 | `adopt-as-is` | Copy verbatim. No changes. |
| 2 | `minimal-patch` | Small edits: rename references, fix paths, adjust formatting. Show diff preview before applying. |
| 3 | `fork-and-extend` | Walk each section interactively: keep / modify / drop / add a new section. |
| 4 | `cherry-pick` | List all sections and let you select which ones to include. |
| 5 | `rewrite` | Use the upstream as inspiration. Draft a new version from scratch; confirm before saving. |

Apply the chosen strategy:
- **adopt-as-is**: no changes to content
- **minimal-patch**: propose changes inline, show before/after diff, confirm
- **fork-and-extend**: for each heading/section, ask: keep / modify / drop / add
- **cherry-pick**: show numbered section list, user selects by number
- **rewrite**: summarize the upstream intent, draft new content, show to user for approval

### 6. Handle name collisions

Check whether a file already exists at the local target path. If it does:

> A file already exists at `.claude/skills/brainstorming/SKILL.md`.
> Choose: **overwrite** / **rename** / **cancel**

- **overwrite**: replace the existing file
- **rename**: prompt for a new name; use it for both the file path and the `upstream.yaml`
- **cancel**: abort, clean up, exit

Never overwrite silently.

### 7. Show review summary

Before writing anything to disk, show:

1. The final adapted file content
2. The `upstream.yaml` provenance file that will be written (see schema below)
3. The target file path
4. The `sources.json` changes (new entry or updated `last_imported`)

Ask for explicit confirmation: **"Write these files? (yes/no)"**

If no, clean up and exit.

### 8. Write files to disk

Create the target directory if it does not exist:
```bash
mkdir -p <target-directory>
```

Write:
1. The capability file at the target path
2. The provenance sidecar alongside it. For capabilities in their own directory (skills: `skills/<name>/`), name it `upstream.yaml`. For capabilities that are flat files in a shared directory (agents: `.claude/agents/<name>.md`, hooks, contexts), name it `<name>.upstream.yaml` in the same directory — this avoids collisions when multiple capabilities of the same type are imported.

### 9. Update `sources.json`

- If the source repo is not yet tracked: add a new entry. For ad-hoc URLs, prompt the user for a short `name` and `description`; set `branch` to `main` and `last_imported` to today's date (ISO 8601).
- If already tracked: update `last_imported` to today's date.

## Provenance Sidecar Schema

The sidecar file is named `upstream.yaml` (for skills in their own directory) or `<name>.upstream.yaml` (for flat-file capabilities like agents, hooks, contexts).

```yaml
source:
  repo: obra/superpowers          # owner/repo (derived from URL)
  path: skills/brainstorming/SKILL.md
  commit: abc123def456            # HEAD commit at import time
  date: 2026-03-28                # ISO 8601 today

adaptation:
  strategy: minimal-patch         # adopt-as-is | minimal-patch | fork-and-extend | cherry-pick | rewrite
  summary: |
    Describe what was changed and why.
  sections_preserved:
    - "Overview"
  sections_modified:
    - "Integration: updated skill references"
  sections_added: []

last_synced: 2026-03-28
```

For `adopt-as-is`: `sections_preserved` lists all top-level headings from the file; `sections_modified` and `sections_added` are `[]`.
For `rewrite`: all three section lists are `[]`; describe what was taken as inspiration in `summary`.

## Error Handling

| Condition | Response |
|---|---|
| `<capability-path>` not found in upstream | Report error. Suggest `/explore <source>` to browse available paths. |
| Clone failure | Report error with git output. Suggest verifying URL and network. |
| User cancels at confirmation | Clean up temp files and exit cleanly. |
| Unknown source name | List names from `sources.json`. |
