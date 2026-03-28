# /sync — First-time sync

## Step 1: Load tracked sources

Read `sources.json`. Found 3 tracked repositories, all with `last_checked: null` — this is a first-time sync.

| # | Name | URL | Branch | Last checked |
|---|---|---|---|---|
| 1 | superpowers | https://github.com/obra/superpowers | main | null |
| 2 | wshobson-agents | https://github.com/wshobson/agents | main | null |
| 3 | everything-claude-code | https://github.com/affaan-m/everything-claude-code | main | null |

---

## Step 2: Fetch recent commits (first-time sync — `per_page=30`, no `since` parameter)

Because all `last_checked` values are null, the first-time sync path is used:

```bash
gh api "repos/obra/superpowers/commits?sha=main&per_page=30"
gh api "repos/wshobson/agents/commits?sha=main&per_page=30"
gh api "repos/affaan-m/everything-claude-code/commits?sha=main&per_page=30"
```

### obra/superpowers

Latest commit: `a3f8d12` — "add brainstorm skill v2 with structured output" (2026-03-20)

30 commits fetched. Changed file paths across all commits (sample):

- `skills/brainstorm/SKILL.md`
- `skills/plan/SKILL.md`
- `skills/tdd/SKILL.md`
- `skills/debug/SKILL.md`
- `skills/execute/SKILL.md`
- `skills/code-review/SKILL.md`
- `README.md`
- `CLAUDE.md`

### wshobson/agents

Latest commit: `c9e2b47` — "update incident-response agent with runbook integration" (2026-03-18)

30 commits fetched. Changed file paths across all commits (sample):

- `agents/code-reviewer/agent.md`
- `agents/incident-response/agent.md`
- `agents/parallel-debugger/agent.md`
- `agents/python-specialist/agent.md`
- `skills/debug-parallel/SKILL.md`
- `README.md`

### affaan-m/everything-claude-code

Latest commit: `7b1a9e3` — "add project detection hooks and session manager" (2026-03-15)

30 commits fetched. Changed file paths across all commits (sample):

- `hooks/pre-commit.sh`
- `hooks/session-manager.md`
- `agents/project-detector/agent.md`
- `rules/coding-standards.md`
- `README.md`

---

## Step 3: Find imported capabilities

```bash
find . -name "upstream.yaml" -not -path "./.git/*"
```

**Result: No `upstream.yaml` files found.**

No capabilities have been imported yet. There is nothing to check for updates.

---

## Sync report — 2026-03-28

> **No imported capabilities found. Run /import first.**

This is a first-time sync — the commit baseline has been established for all 3 sources, but there are no imported capabilities to check for updates.

---

## Step 7: Update `sources.json`

All three sources have been checked. Update each entry:
- `last_checked` → `"2026-03-28"`
- `last_checked_commit` → latest commit SHA fetched

Updated `sources.json`:

```json
{
  "version": 1,
  "repositories": [
    {
      "name": "superpowers",
      "url": "https://github.com/obra/superpowers",
      "branch": "main",
      "description": "Workflow skills: brainstorm, plan, execute, TDD, debug, code review",
      "last_checked": "2026-03-28",
      "last_checked_commit": "a3f8d12",
      "last_imported": null
    },
    {
      "name": "wshobson-agents",
      "url": "https://github.com/wshobson/agents",
      "branch": "main",
      "description": "Agent teams, parallel debugging, incident response, python skills",
      "last_checked": "2026-03-28",
      "last_checked_commit": "c9e2b47",
      "last_imported": null
    },
    {
      "name": "everything-claude-code",
      "url": "https://github.com/affaan-m/everything-claude-code",
      "branch": "main",
      "description": "Hooks, agents, session management, rules, project detection",
      "last_checked": "2026-03-28",
      "last_checked_commit": "7b1a9e3",
      "last_imported": null
    }
  ]
}
```

---

## Summary

- 3 sources checked (first-time sync — `per_page=30`, no `since` parameter)
- 0 imported capabilities found
- Commit baselines recorded for all 3 sources
- Next sync will use `since=2026-03-28` and `per_page=100` to fetch only new commits

Run `/import` to bring in capabilities from your tracked sources, then `/sync` again to track future updates.
