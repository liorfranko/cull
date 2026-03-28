# cull

A plugin manager for Claude Code. Cut through the noise — browse, import, and track skills and agents from any public GitHub repo.

---

## The problem

Every day someone posts "this skill will boost your Claude Code by 10x." There's no standard way to browse what's actually available, compare it to what you already have, import selectively, or track updates.

`cull` is that layer.

## What it does

- **`/explore`** — give it any GitHub repo URL (or a name from your tracked sources), and it shows you exactly what capabilities are inside: skills, agents, and hooks. Compared against what you already have locally.
- **`/import`** — pick a capability, choose how to adapt it (verbatim copy, light edits, section-by-section, or full rewrite), and it places the file in the right `.claude/` directory with a provenance sidecar so you always know where it came from.
- **`/sync`** — check all your tracked sources for updates to capabilities you've already imported. Get a per-capability report and decide: apply the update, skip it, or flag for manual review.

## Install

```
/plugin marketplace add https://github.com/liorfranko/cull.git
/plugin install cull
```

## Usage

```
# See what a repo has to offer
/explore https://github.com/obra/superpowers

# Or use a tracked source name
/explore superpowers

# Import a specific capability
/import superpowers skills/brainstorming/SKILL.md

# Import from any repo
/import https://github.com/wshobson/agents agents/code-reviewer.md
```

## Tracked sources

`sources.json` ships with five curated starter sources:

| Name | Repo | What's in it |
|---|---|---|
| `superpowers` | obra/superpowers | Workflow skills: brainstorm, plan, TDD, debug, code review |
| `wshobson-agents` | wshobson/agents | Agent teams, parallel debugging, incident response |
| `everything-claude-code` | affaan-m/everything-claude-code | Hooks, agents, session management, rules |
| `anthropics-skills` | anthropics/skills | Official Anthropic skills: docx, pdf, pptx, frontend-design, claude-api, mcp-builder |
| `awesome-claude-skills` | ComposioHQ/awesome-claude-skills | Curated community collection: skill-creator, langsmith, brand-guidelines and 25+ more |

Add your own by editing `sources.json`.

## Provenance tracking

Every imported capability gets an `upstream.yaml` sidecar:

```yaml
source:
  repo: obra/superpowers
  path: skills/brainstorming/SKILL.md
  commit: abc123def456
  date: 2026-03-28

adaptation:
  strategy: minimal-patch
  summary: Updated skill references to local paths.
  sections_preserved: ["Overview", "Key Principles"]
  sections_modified: ["Integration"]
  sections_added: []

last_synced: 2026-03-28
```

You always know where a capability came from, at what commit, and how it was adapted.

## Fork it

`cull` is designed to be forked. The marketplace infrastructure (`.claude-plugin/`, `plugins/`, `sources.json`) is the template — swap in your own skills, point `sources.json` at your org's repos, and you have a private capability registry for your team.

## License

MIT
