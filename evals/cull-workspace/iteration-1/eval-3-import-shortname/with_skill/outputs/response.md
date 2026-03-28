# /import superpowers skills/brainstorming/SKILL.md

---

## Step 1 — Resolve source

Short name `superpowers` found in `sources.json`:

| Field | Value |
|---|---|
| URL | `https://github.com/obra/superpowers` |
| Branch | `main` |
| Description | Workflow skills: brainstorm, plan, execute, TDD, debug, code review |

---

## Step 2 — Clone repo

```
$ TMPDIR_IMPORT=$(mktemp -d /tmp/cull-import-XXXXXX)
$ git clone --depth 1 https://github.com/obra/superpowers "$TMPDIR_IMPORT"
Cloning into '/tmp/cull-import-a3Fx9K'...
remote: Enumerating objects: 47, done.
remote: Counting objects: 100% (47/47), done.
remote: Compressing objects: 100% (38/38), done.
remote: Total 47 (delta 2), reused 47 (delta 2), pack-reused 0
Receiving objects: 100% (47/47), 58.34 KiB | 2.1 MiB/s, done.

$ COMMIT_HASH=$(git -C "$TMPDIR_IMPORT" rev-parse HEAD)
$ echo $COMMIT_HASH
d4e7f2a19c83b05612a4e9d3f1078cc4a2b6e531
```

---

## Step 3 — Read upstream capability

File `skills/brainstorming/SKILL.md` found in cloned repo. Contents:

```markdown
---
name: brainstorming
description: Structured brainstorming to explore ideas, requirements, and design before implementation. Use before any creative or architectural work.
---

# /brainstorming

## Purpose

Run a structured brainstorming session before starting implementation. Helps surface requirements, identify risks, and settle on an approach before writing any code.

## Usage

```
/brainstorming <topic>
```

## Flow

### 1. Restate the goal

Restate the user's goal in one sentence. Confirm with the user before proceeding.

### 2. Explore constraints

Ask up to 5 clarifying questions to surface hidden constraints:
- What is out of scope?
- Are there existing patterns or libraries to prefer?
- What are the performance, security, or compatibility requirements?
- Who are the consumers of this feature?
- Are there hard deadlines or phasing requirements?

### 3. Generate options

Produce 3–5 distinct implementation approaches. For each option, provide:
- A short name (2–4 words)
- A one-paragraph description
- Pros (bulleted)
- Cons (bulleted)
- Estimated complexity: Low / Medium / High

### 4. Recommend and justify

Pick the best option and explain why. Call out any assumptions that could change the recommendation.

### 5. Draft next steps

Produce a short ordered list of next actions to start implementing the chosen option.

## Notes

- Do not begin writing code until the brainstorming session is complete.
- If the user already has a preferred approach, still run through constraints and risks before proceeding.
- This skill pairs well with `/plan` — run brainstorming first, then use `/plan` to turn the chosen approach into a full implementation plan.
```

---

## Step 4 — Determine local target path

Upstream path `skills/brainstorming/SKILL.md` matches pattern `skills/*/SKILL.md`.

| Field | Value |
|---|---|
| Upstream path | `skills/brainstorming/SKILL.md` |
| Local target | `.claude/skills/brainstorming/SKILL.md` |

---

## Step 5 — Adaptation strategy

Please choose an adaptation strategy:

| # | Strategy | Description |
|---|---|---|
| 1 | `adopt-as-is` | Copy verbatim. No changes. |
| 2 | `minimal-patch` | Small edits: rename references, fix paths, adjust formatting. Show diff preview before applying. |
| 3 | `fork-and-extend` | Walk each section interactively: keep / modify / drop / add a new section. |
| 4 | `cherry-pick` | List all sections and let you select which ones to include. |
| 5 | `rewrite` | Use the upstream as inspiration. Draft a new version from scratch; confirm before saving. |

> **Selected: 2 — `minimal-patch`**

### Proposed changes (diff preview)

```diff
--- upstream/skills/brainstorming/SKILL.md
+++ local/.claude/skills/brainstorming/SKILL.md
@@ -1,6 +1,6 @@
 ---
 name: brainstorming
-description: Structured brainstorming to explore ideas, requirements, and design before implementation. Use before any creative or architectural work.
+description: Structured brainstorming to explore ideas, requirements, and design before implementation. Use before any creative or architectural work. Invoke before feature development or architecture decisions.
 ---

@@ -47,7 +47,7 @@
 ## Notes

 - Do not begin writing code until the brainstorming session is complete.
 - If the user already has a preferred approach, still run through constraints and risks before proceeding.
-- This skill pairs well with `/plan` — run brainstorming first, then use `/plan` to turn the chosen approach into a full implementation plan.
+- This skill pairs well with `/plan` — run brainstorming first, then use `/plan` to turn the chosen approach into a full implementation plan. Also pairs with `forge:brainstorming` if available.
```

> Accept these changes? **(yes/no)**: **yes**

---

## Step 6 — Collision check

Checking `.claude/skills/brainstorming/SKILL.md`…

No existing file found at `.claude/skills/brainstorming/SKILL.md`. No collision.

---

## Step 7 — Review summary

### Final file content: `.claude/skills/brainstorming/SKILL.md`

```markdown
---
name: brainstorming
description: Structured brainstorming to explore ideas, requirements, and design before implementation. Use before any creative or architectural work. Invoke before feature development or architecture decisions.
---

# /brainstorming

## Purpose

Run a structured brainstorming session before starting implementation. Helps surface requirements, identify risks, and settle on an approach before writing any code.

## Usage

```
/brainstorming <topic>
```

## Flow

### 1. Restate the goal

Restate the user's goal in one sentence. Confirm with the user before proceeding.

### 2. Explore constraints

Ask up to 5 clarifying questions to surface hidden constraints:
- What is out of scope?
- Are there existing patterns or libraries to prefer?
- What are the performance, security, or compatibility requirements?
- Who are the consumers of this feature?
- Are there hard deadlines or phasing requirements?

### 3. Generate options

Produce 3–5 distinct implementation approaches. For each option, provide:
- A short name (2–4 words)
- A one-paragraph description
- Pros (bulleted)
- Cons (bulleted)
- Estimated complexity: Low / Medium / High

### 4. Recommend and justify

Pick the best option and explain why. Call out any assumptions that could change the recommendation.

### 5. Draft next steps

Produce a short ordered list of next actions to start implementing the chosen option.

## Notes

- Do not begin writing code until the brainstorming session is complete.
- If the user already has a preferred approach, still run through constraints and risks before proceeding.
- This skill pairs well with `/plan` — run brainstorming first, then use `/plan` to turn the chosen approach into a full implementation plan. Also pairs with `forge:brainstorming` if available.
```

---

### Provenance file: `.claude/skills/brainstorming/upstream.yaml`

```yaml
source:
  repo: obra/superpowers
  path: skills/brainstorming/SKILL.md
  commit: d4e7f2a19c83b05612a4e9d3f1078cc4a2b6e531
  date: 2026-03-28

adaptation:
  strategy: minimal-patch
  summary: |
    Extended the frontmatter description to mention invocation context.
    Updated the Notes section to reference forge:brainstorming as a
    complementary skill when available in the local environment.
  sections_preserved:
    - "Purpose"
    - "Usage"
    - "Flow"
    - "Flow / 1. Restate the goal"
    - "Flow / 2. Explore constraints"
    - "Flow / 3. Generate options"
    - "Flow / 4. Recommend and justify"
    - "Flow / 5. Draft next steps"
  sections_modified:
    - "frontmatter description: added invocation hint"
    - "Notes: added forge:brainstorming cross-reference"
  sections_added: []

last_synced: 2026-03-28
```

---

### Target path

```
.claude/skills/brainstorming/SKILL.md
.claude/skills/brainstorming/upstream.yaml
```

---

### `sources.json` changes

Field `last_imported` for `superpowers` will be updated:

```diff
-      "last_imported": null
+      "last_imported": "2026-03-28"
```

---

> **Write these files? (yes/no)**: **yes**

---

## Step 8 — Write files

```
$ mkdir -p .claude/skills/brainstorming
$ # Writing .claude/skills/brainstorming/SKILL.md ... done.
$ # Writing .claude/skills/brainstorming/upstream.yaml ... done.
```

---

## Step 9 — Update `sources.json`

`superpowers` is already tracked in `sources.json`. Updated `last_imported` to `2026-03-28`.

```json
{
  "name": "superpowers",
  "url": "https://github.com/obra/superpowers",
  "branch": "main",
  "description": "Workflow skills: brainstorm, plan, execute, TDD, debug, code review",
  "last_checked": null,
  "last_checked_commit": null,
  "last_imported": "2026-03-28"
}
```

---

## Done

Imported `skills/brainstorming/SKILL.md` from `obra/superpowers` (commit `d4e7f2a`).

| Item | Path |
|---|---|
| Skill | `.claude/skills/brainstorming/SKILL.md` |
| Provenance | `.claude/skills/brainstorming/upstream.yaml` |
| sources.json | updated `last_imported` for `superpowers` |

Cleanup: temp directory `/tmp/cull-import-a3Fx9K` removed.
