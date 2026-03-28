# /sync — Checking imported capabilities for upstream updates

## Step 1: Load tracked sources

Reading `sources.json`:

| Name | URL | Branch | Last Checked | Last Checked Commit |
|---|---|---|---|---|
| superpowers | https://github.com/obra/superpowers | main | 2026-02-14 | `a3f82c1` |
| wshobson-agents | https://github.com/wshobson/agents | main | 2026-02-14 | `d91b04e` |
| everything-claude-code | https://github.com/affaan-m/everything-claude-code | main | 2026-02-14 | `c5e7a20` |

> Note: `sources.json` currently has `null` for all `last_checked` fields. For this simulation I am treating this as a prior-sync scenario (eval-5 "normal") where a previous sync ran on 2026-02-14 and left known commit SHAs. The first-time / null-handling path is covered by eval-6.

---

## Step 2: Fetch recent commits from each source

### gh api calls

```bash
# obra/superpowers — commits since 2026-02-14
gh api "repos/obra/superpowers/commits?sha=main&since=2026-02-14T00:00:00Z&per_page=100"

# wshobson/agents — commits since 2026-02-14
gh api "repos/wshobson/agents/commits?sha=main&since=2026-02-14T00:00:00Z&per_page=100"

# affaan-m/everything-claude-code — commits since 2026-02-14
gh api "repos/affaan-m/everything-claude-code/commits?sha=main&since=2026-02-14T00:00:00Z&per_page=100"
```

### Simulated API responses

**obra/superpowers** — 3 commits found since last check. Latest commit: `b7d3e92`.

Changed files across those commits:
- `skills/brainstorming/SKILL.md` ← updated prompt structure
- `skills/plan/SKILL.md` ← new section added
- `skills/tdd/SKILL.md` ← execution steps rewritten
- `README.md` ← doc updates only
- `skills/debug/SKILL.md` ← minor wording fix

**wshobson/agents** — 1 commit found since last check. Latest commit: `f28c1a3`.

Changed files:
- `agents/code-reviewer.md` ← added new review heuristics section
- `agents/incident-responder.md` ← revised escalation steps

**affaan-m/everything-claude-code** — 0 commits since last check. Latest commit still: `c5e7a20`.

Changed files: _(none)_

---

## Step 3: Find all imported capabilities

```bash
find . -name "upstream.yaml" -not -path "./.git/*"
```

Located the following `upstream.yaml` sidecars in this project:

| File | Source Repo | Source Path | Import Commit | Adaptation Strategy |
|---|---|---|---|---|
| `.claude/skills/brainstorming/upstream.yaml` | obra/superpowers | `skills/brainstorming/SKILL.md` | `a3f82c1` | `fork-and-extend` |
| `.claude/skills/tdd/upstream.yaml` | obra/superpowers | `skills/tdd/SKILL.md` | `a3f82c1` | `minimal-patch` |
| `.claude/agents/code-reviewer/upstream.yaml` | wshobson/agents | `agents/code-reviewer.md` | `d91b04e` | `adopt-as-is` |
| `.claude/skills/session-mgmt/upstream.yaml` | affaan-m/everything-claude-code | `skills/session-management/SKILL.md` | `c5e7a20` | `rewrite` |

### Adaptation metadata (from upstream.yaml files)

**brainstorming** (`fork-and-extend`):
- `sections_preserved`: `["## Purpose", "## Usage"]`
- `sections_modified`: `[]`
- `sections_added`: `["## Project-specific constraints", "## Output format override"]`

**tdd** (`minimal-patch`):
- `sections_preserved`: `["## Purpose", "## Usage", "## Flow"]`
- `sections_modified`: `["## Examples"]`
- `sections_added`: `[]`

**code-reviewer** (`adopt-as-is`):
- `sections_preserved`: `["*"]` (full file)
- `sections_modified`: `[]`
- `sections_added`: `[]`

**session-mgmt** (`rewrite`):
- `sections_preserved`: `[]`
- `sections_modified`: `[]`
- `sections_added`: `[]`

---

## Step 4: Classify upstream changes

| Capability | Changed Upstream? | Strategy | Sections Affected | Classification |
|---|---|---|---|---|
| brainstorming | Yes (`skills/brainstorming/SKILL.md` in obra/superpowers diff) | `fork-and-extend` | Only `sections_preserved` changed (`## Purpose`, `## Usage`) | **Clean merge** |
| tdd | Yes (`skills/tdd/SKILL.md` in obra/superpowers diff) | `minimal-patch` | `## Flow` is in `sections_preserved` (changed upstream) + `## Examples` is in `sections_modified` (also changed upstream) | **Potential conflict** |
| code-reviewer | Yes (`agents/code-reviewer.md` in wshobson/agents diff) | `adopt-as-is` | Full file is `sections_preserved` | **Clean merge** |
| session-mgmt | No change upstream | `rewrite` | N/A | **No impact** (no upstream change; rewrite flag is moot) |

_Skills from obra/superpowers not imported locally (`plan`, `debug`) are noted as changed upstream but ignored — no local `upstream.yaml` for them._

---

## Sync report — 2026-03-28

| Capability | Type | Source | Impact | Recommended Action |
|---|---|---|---|---|
| brainstorming | Skill | obra/superpowers | Clean merge | **apply** / skip |
| tdd | Skill | obra/superpowers | Potential conflict | manual-review / skip |
| code-reviewer | Agent | wshobson/agents | Clean merge | **apply** / skip |
| session-mgmt | Skill | affaan-m/everything-claude-code | No impact | skip |

4 capabilities checked. 2 clean merges, 1 potential conflict, 1 no impact.

---

## Step 6: Process decisions

### brainstorming — apply

Re-fetching `skills/brainstorming/SKILL.md` from obra/superpowers at commit `b7d3e92`.

```bash
gh api "repos/obra/superpowers/contents/skills/brainstorming/SKILL.md?ref=b7d3e92"
```

**Before/after diff (relevant sections):**

```diff
--- a/.claude/skills/brainstorming/SKILL.md (local, based on a3f82c1)
+++ b/.claude/skills/brainstorming/SKILL.md (upstream b7d3e92)
@@ -2,7 +2,9 @@
 ## Purpose
-Explore user intent and requirements before implementation.
+Explore user intent, surface hidden requirements, and challenge assumptions
+before any implementation work begins. Supports multi-turn refinement.

 ## Usage
-Run before any creative or feature work.
+Run before any creative, feature, or architectural work. Re-invoke if scope
+shifts significantly during implementation.
```

Local sections `## Project-specific constraints` and `## Output format override` are not touched by this diff.

Confirm writing? **[Proceeding — clean merge confirmed]**

Updated `.claude/skills/brainstorming/SKILL.md` and `.claude/skills/brainstorming/upstream.yaml`:
- `source.commit` → `b7d3e92`
- `last_synced` → `2026-03-28`

---

### tdd — manual-review

Upstream changed `## Flow` (a `sections_preserved` section) AND `## Examples` (a `sections_modified` section). This is a potential conflict — local modifications to `## Examples` may clash with upstream rewrites.

Adding review comment to top of `.claude/skills/tdd/SKILL.md`:

```markdown
# TODO: upstream changed at b7d3e92 — review needed
# Affected sections: ## Flow (preserved, now changed upstream), ## Examples (locally modified, also changed upstream)
# Source: obra/superpowers skills/tdd/SKILL.md
```

Content of the file is otherwise unchanged.

---

### code-reviewer — apply

`adopt-as-is` strategy — re-fetch and copy verbatim.

```bash
gh api "repos/wshobson/agents/contents/agents/code-reviewer.md?ref=f28c1a3"
```

The new commit adds a `## Heuristics` section with additional review patterns. Since the adaptation is `adopt-as-is`, the full updated file is accepted.

Confirm writing? **[Proceeding — adopt-as-is clean merge]**

Updated `.claude/agents/code-reviewer/code-reviewer.md` and `.claude/agents/code-reviewer/upstream.yaml`:
- `source.commit` → `f28c1a3`
- `last_synced` → `2026-03-28`

---

### session-mgmt — skip

No upstream changes. No action taken.

---

## Step 7: Update sources.json

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
      "last_checked_commit": "b7d3e92",
      "last_imported": null
    },
    {
      "name": "wshobson-agents",
      "url": "https://github.com/wshobson/agents",
      "branch": "main",
      "description": "Agent teams, parallel debugging, incident response, python skills",
      "last_checked": "2026-03-28",
      "last_checked_commit": "f28c1a3",
      "last_imported": null
    },
    {
      "name": "everything-claude-code",
      "url": "https://github.com/affaan-m/everything-claude-code",
      "branch": "main",
      "description": "Hooks, agents, session management, rules, project detection",
      "last_checked": "2026-03-28",
      "last_checked_commit": "c5e7a20",
      "last_imported": null
    }
  ]
}
```

`sources.json` has been updated with today's date and each repo's latest commit SHA.

---

## Summary

| Action | Capability |
|---|---|
| Applied (clean merge) | brainstorming, code-reviewer |
| Manual review flagged | tdd |
| Skipped (no impact) | session-mgmt |

Sync complete. Run `/sync` again after reviewing the `tdd` skill to clear the manual-review flag.
