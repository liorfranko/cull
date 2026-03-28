# /explore superpowers

## Step 1: Resolve source

Short name `superpowers` found in `sources.json`:

```
url:    https://github.com/obra/superpowers
branch: main
```

## Step 2: Clone repo

```bash
TMPDIR_CLONE=$(mktemp -d /tmp/cull-explore-XXXXXX)
timeout 60 git clone --depth 1 https://github.com/obra/superpowers "$TMPDIR_CLONE"
```

```
Cloning into '/tmp/cull-explore-a3Kx8f'...
remote: Enumerating objects: 87, done.
remote: Counting objects: 100% (87/87), done.
remote: Compressing objects: 100% (61/61), done.
Receiving objects: 100% (87/87), 142.3 KiB | 1.8 MiB/s, done.
```

## Step 3: Scan for capabilities

Scanning patterns:
- `skills/*/SKILL.md` → Skills
- `agents/*.md` → Agents
- `hooks/**/*.js` → Hooks
- `rules/**/*.md` → Rules
- `contexts/*.md` → Contexts

Found 9 capabilities.

## Step 4: Compare against local `.claude/`

Local skills present: careful, codex, design-consultation, design-review, document-release, freeze, guard, investigate, office-hours, plan-ceo-review, plan-design-review, plan-eng-review, pr-report, qa, qa-only, release, release-changelog, retro, review, ship, unfreeze, vault-task-executor

No local agents, hooks, rules, or contexts found.

---

## Capabilities found in obra/superpowers

### New (available to import)

| Type | Name | Path | Description |
|---|---|---|---|
| Skill | brainstorm | skills/brainstorm/SKILL.md | Structured ideation before building — explores requirements and constraints through dialogue |
| Skill | plan | skills/plan/SKILL.md | Produce a written implementation plan from a spec or task description |
| Skill | execute | skills/execute/SKILL.md | Execute a plan step-by-step with checkpoints and verification |
| Skill | tdd | skills/tdd/SKILL.md | Test-driven development loop — write tests first, then implement to pass them |
| Skill | debug | skills/debug/SKILL.md | Systematic debugging: reproduce, isolate, hypothesize, verify, fix |
| Agent | spec-writer | agents/spec-writer.md | Turns a rough idea into a structured spec with acceptance criteria |
| Agent | reviewer | agents/reviewer.md | Reviews code for correctness, clarity, and consistency with project conventions |
| Rule | commit-hygiene | rules/git/commit-hygiene.md | Enforce atomic commits with descriptive messages |

### Already have (existing)

| Type | Name | Local Path | Status |
|---|---|---|---|
| Skill | review | skills/review | existing |

### Can upgrade

| Type | Name | Local Path | Upstream Path | Note |
|---|---|---|---|---|
| Skill | retro | skills/retro | skills/retro/SKILL.md | Upstream version updated — adds structured action-item extraction |

---

**Total: 9 capabilities. 8 new, 1 existing, 1 upgradable.**

---

## What you can upgrade

The `retro` skill in your local `.claude/skills/retro/SKILL.md` differs from the upstream version in `obra/superpowers`. The upstream version adds a structured action-item extraction phase at the end of retrospectives, producing a prioritized list of improvements with owners and due dates.

Run `/import superpowers retro` to upgrade it.

## Step 6: Cleanup

```bash
rm -rf /tmp/cull-explore-a3Kx8f
```

Done.
