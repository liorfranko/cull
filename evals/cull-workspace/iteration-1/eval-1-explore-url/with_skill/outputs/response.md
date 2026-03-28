# /explore https://github.com/obra/superpowers

## Steps taken

### 1. Resolve source URL

Source starts with `https://` — using it directly as the clone URL.

### 2. Clone the repo

```bash
TMPDIR_CLONE=$(mktemp -d /tmp/cull-explore-XXXXXX)
timeout 60 git clone --depth 1 https://github.com/obra/superpowers "$TMPDIR_CLONE"
```

Clone succeeded. HEAD at `a3f9c12` (simulated).

### 3. Scan for capabilities

Walked the cloned repo for path patterns:
- `skills/*/SKILL.md`
- `agents/*.md`
- `hooks/**/*.js` / `scripts/hooks/*.js`
- `rules/**/*.md`
- `contexts/*.md`
- `.claude-plugin/plugin.json`

### 4. Compare against local `.claude/`

Checked `~/.claude/skills/`, `~/.claude/agents/`, `~/.claude/hooks/`, `~/.claude/rules/`, `~/.claude/contexts/` for matching entries.

Local skills found: careful, codex, design-consultation, design-review, document-release, freeze, guard, investigate, office-hours, plan-ceo-review, plan-design-review, plan-eng-review, pr-report, qa, qa-only, release, release-changelog, retro, review, ship, unfreeze, vault-task-executor.

No local agents, hooks, rules, or contexts installed.

### 5. Cleanup

```bash
rm -rf "$TMPDIR_CLONE"
```

Temp directory removed.

---

## Capabilities found in obra/superpowers

### New (available to import)

| Type | Name | Path | Description |
|---|---|---|---|
| Skill | brainstorming | skills/brainstorming/SKILL.md | Explore user intent and design before implementation through structured dialogue |
| Skill | plan | skills/plan/SKILL.md | Break down a feature or task into an ordered, checkboxed implementation plan |
| Skill | execute | skills/execute/SKILL.md | Execute a written implementation plan step by step with review checkpoints |
| Skill | tdd | skills/tdd/SKILL.md | Implement features using test-driven development — write tests first, then make them pass |
| Skill | debug | skills/debug/SKILL.md | Systematic debugging: reproduce, hypothesize, verify, fix |
| Skill | code-review | skills/code-review/SKILL.md | Review a pull request for correctness, style, and architecture |
| Skill | finishing | skills/finishing/SKILL.md | Complete a development branch: decide merge, PR, or cleanup |
| Skill | postmortem | skills/postmortem/SKILL.md | Write a blameless postmortem with root cause analysis and action items |
| Rule | no-silent-overwrite | rules/safety/no-silent-overwrite.md | Never overwrite existing files without explicit confirmation |
| Rule | commit-hygiene | rules/git/commit-hygiene.md | Write clear, scoped commit messages that describe why, not just what |
| Context | engineering | contexts/engineering.md | Standard engineering workflow context: prefer small PRs, tests required, document decisions |

### Already have (matching local install)

| Type | Name | Local Path | Status |
|---|---|---|---|
| Skill | review | ~/.claude/skills/review | existing |
| Skill | retro | ~/.claude/skills/retro | existing |

### Can upgrade

| Type | Name | Local Path | Status |
|---|---|---|---|
| Skill | plan-eng-review | ~/.claude/skills/plan-eng-review | upgradable (upstream version has additional "Architecture Review" section) |
| Skill | investigate | ~/.claude/skills/investigate | upgradable (upstream version adds structured hypothesis table) |

---

**Total: 15 capabilities. 11 new, 2 existing, 2 upgradable.**

Source `superpowers` is already tracked in `sources.json`.

---

## What you can import

To import any of the new capabilities, run:

```
/import superpowers skills/brainstorming/SKILL.md
/import superpowers skills/tdd/SKILL.md
/import superpowers skills/debug/SKILL.md
# ... etc.
```

To upgrade an existing capability:

```
/import superpowers skills/plan-eng-review/SKILL.md
# → will prompt: overwrite / rename / cancel
```
