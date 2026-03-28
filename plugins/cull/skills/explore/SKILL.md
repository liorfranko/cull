---
name: explore
description: Use this skill to explore a GitHub repo and discover importable Claude Code capabilities (skills, agents, hooks) — compares what the repo has against your local .claude/ directory and classifies each capability as new, existing, or upgradable. Invoke whenever the user wants to browse, scan, or see what a GitHub repo has to offer for Claude Code.
---

# /explore

## Purpose

Discover what a GitHub repository offers in terms of Claude Code capabilities — skills, agents, and hooks — and compare against what you already have locally. The output tells you exactly what is new, what you already have, and what can be upgraded.

## Usage

```
/explore <source>
```

Where `<source>` is either:
- A full GitHub HTTPS URL: `https://github.com/obra/superpowers`
- A short name from `sources.json`: `superpowers`

## Flow

### 1. Resolve the source URL

- If `<source>` starts with `https://`, use it directly as the clone URL.
- If `<source>` is a short name, look for `sources.json` in the current working directory (walk up parent directories if not found). Look up the matching entry's `url` field.
- If neither matches, print usage and exit.

### 2. Clone the repo

```bash
TMPDIR_CLONE=$(mktemp -d /tmp/cull-explore-XXXXXX)
timeout 60 git clone --depth 1 <url> "$TMPDIR_CLONE"
```

- If `timeout` exits with code 124, report timeout, clean up, and exit.
- On any clone failure (auth error, repo not found, timeout), report the error and suggest checking the URL.

### 3. Scan for capabilities

Walk the cloned repo for these path patterns (in order):

| Pattern | Type |
|---|---|
| `skills/*/SKILL.md` | Skill |
| `agents/*.md` | Agent |
| `hooks/**/*.js` or `scripts/hooks/*.js` | Hook |
| `.claude-plugin/plugin.json` | Plugin |

For each match, extract:
- **name** — directory or filename (without extension)
- **type** — from the table above
- **path** — relative path in the upstream repo
- **description** — from YAML frontmatter `description:` field if present; otherwise the first non-empty non-heading line of the file

### 4. Compare against local `.claude/`

For each discovered capability, check whether a file exists locally at the corresponding path:

| Type | Local path to check |
|---|---|
| Skill | `.claude/skills/<name>/SKILL.md` |
| Agent | `.claude/agents/<name>.md` |
| Hook | `.claude/hooks/<name>.js` |

Classification:
- **new** — no local file with matching type + name
- **existing** — local file exists and content is identical to upstream
- **upgradable** — local file exists but content differs from upstream

### 5. Output the report

```
## Capabilities found in <owner/repo>

### New (available to import)
| Type | Name | Path | Description |
|---|---|---|---|
| Skill | brainstorming | skills/brainstorming/SKILL.md | Design through dialogue |

### Already have (existing)
| Type | Name | Local Path | Status |
|---|---|---|---|
| Skill | tdd | skills/tdd | existing |

### Can upgrade
| Type | Name | Local Path | Status |
|---|---|---|---|
| Skill | debug | skills/debug | upgradable |

Total: X capabilities. Y new, Z existing, W upgradable.
```

If the source is not yet tracked in `sources.json`, append:
```
Source '<name>' is not yet tracked in sources.json. It will be added automatically when you run /import.
```

If zero capabilities were found, say so and list the patterns that were checked.

### 6. Cleanup

Always remove the temp clone regardless of outcome:

```bash
rm -rf "$TMPDIR_CLONE"
```

## Error Handling

| Condition | Response |
|---|---|
| Clone timeout (>60s) | Report timeout. Suggest verifying the URL and network access. |
| Repo not found / auth failure | Report error. Suggest `gh auth login` or checking the URL. |
| Short name not in `sources.json` | List available names from `sources.json`. |
| Zero capabilities found | List patterns checked. Suggest the repo may use a non-standard layout. |
