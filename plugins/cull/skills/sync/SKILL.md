---
name: sync
description: Check all tracked sources in sources.json for updates to previously imported capabilities and present a sync report with apply / skip / manual-review actions
---

# /sync

## Purpose

Check whether upstream repositories have published updates to capabilities you have previously imported. When updates are found, present a structured report and help you apply, skip, or flag changes for manual review.

Requires `gh` CLI to be authenticated (`gh auth login`).

## Usage

```
/sync
```

No arguments. Operates on all tracked sources in `sources.json` and all `upstream.yaml` files found in the current project.

## Flow

### 1. Load tracked sources

Read `sources.json` at the project root. For each entry, note:
- `url` — the upstream repo URL
- `last_checked` — the date of the last sync check (may be null)
- `last_checked_commit` — the last known HEAD commit (may be null)
- `branch` — branch to check (default: `main`)

### 2. Fetch recent commits from each source

For each tracked repo, use `gh api` to get commits since `last_checked`:

```bash
gh api "repos/{owner}/{repo}/commits?sha={branch}&since={last_checked_date}&per_page=100"
```

If `last_checked` is null, fetch the last 30 commits (first-time sync).

Record the set of changed file paths across all returned commits.

### 3. Find all imported capabilities

Search the current project for every `upstream.yaml` sidecar:

```bash
find . -name "upstream.yaml" -not -path "./.git/*"
```

For each `upstream.yaml`, read:
- `source.repo` — the upstream owner/repo
- `source.path` — the original file path in the upstream repo
- `source.commit` — the commit at import time
- `adaptation.strategy` — how it was adapted
- `adaptation.sections_preserved` — sections copied verbatim
- `adaptation.sections_modified` — sections with local changes
- `adaptation.sections_added` — sections added locally (no upstream counterpart)

### 4. Classify upstream changes

For each imported capability whose `source.path` appears in the changed files list for its repo:

| Classification | Condition | Safe to auto-apply? |
|---|---|---|
| **Clean merge** | Only `sections_preserved` changed upstream | Yes |
| **Potential conflict** | Any `sections_modified` changed upstream | No — review needed |
| **No impact** | Only `sections_added` have local content; upstream changes don't overlap | Yes |

If the upstream file was **deleted**, flag it as `deleted upstream` — suggest removing or replacing.

### 5. Present the sync report

```
## Sync report — 2026-03-28

| Capability | Type | Source | Impact | Action |
|---|---|---|---|---|
| brainstorming | Skill | obra/superpowers | Clean merge | apply / skip |
| tdd | Skill | obra/superpowers | Potential conflict | manual-review / skip |
| code-reviewer | Agent | wshobson/agents | No impact | skip |

3 capabilities checked. 1 clean merge, 1 potential conflict, 1 no impact.
```

If nothing has changed since the last check, print: "All capabilities are up to date."

### 6. Process decisions

For each capability the user acts on:

- **apply** — re-fetch the upstream file at the latest commit and re-apply the adaptation strategy from `upstream.yaml`. Show the before/after diff. Confirm before writing. Update `upstream.yaml` with the new commit hash and `last_synced` date.
- **skip** — leave unchanged. No metadata updates for this capability.
- **manual-review** — add a `# TODO: upstream changed at <commit> — review needed` comment at the top of the capability file. Do not modify the content otherwise.

### 7. Update `sources.json`

After processing all decisions, update each tracked repo entry:
- `last_checked` → today's ISO 8601 date
- `last_checked_commit` → the latest commit SHA fetched in step 2

## Error Handling

| Condition | Response |
|---|---|
| `gh` not authenticated | Report error. Instruct: `gh auth login`. |
| API rate limit exceeded | Report the limit. Suggest waiting or using `gh auth login` for higher limits. |
| Repo deleted or inaccessible | Warn. Suggest removing the entry from `sources.json`. |
| Force-push / history rewrite | Warn about rewrite. Flag all affected capabilities as `manual-review`. |
| No `upstream.yaml` files found | Report: "No imported capabilities found. Run /import first." |
| `last_checked` is null | Treat as first sync — fetch last 30 commits as baseline. |
