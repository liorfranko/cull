# Baseline (no skill) — Eval 1

Agent was unable to proceed without a structured skill. It attempted gh CLI, WebFetch, and GitHub MCP tools but lacked a defined flow for what to do with a GitHub URL in this context.

**Key observation:** Without the /explore skill, Claude has no structured flow — it reached for arbitrary tool calls but had no defined scanning patterns, comparison logic, or output format.
