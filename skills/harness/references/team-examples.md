# Codex Harness Examples

Use these examples as templates. Keep generated artifacts Codex-native.

## Example 1: Code Review Harness

Pattern: Fan-out/Fan-in

Artifacts:

```text
AGENTS.md
.agents/skills/code-review-orchestrator/SKILL.md
_workspace/
```

Workflow:

1. Parent saves review scope to `_workspace/00_input.md`.
2. Spawn parallel subagents:
   - security reviewer -> `_workspace/01_security.md`
   - correctness reviewer -> `_workspace/01_correctness.md`
   - test reviewer -> `_workspace/01_tests.md`
3. Parent reads all artifacts and writes `_workspace/02_review_summary.md`.
4. Parent reports findings ordered by severity.

## Example 2: API Documentation Harness

Pattern: Pipeline + Producer-Reviewer

Workflow:

1. Explorer subagent maps endpoints and writes `_workspace/01_api_inventory.md`.
2. Writer subagent drafts docs to `_workspace/02_docs_draft.md`.
3. Reviewer subagent checks examples against source code and writes `_workspace/03_docs_review.md`.
4. Parent applies fixes and writes final docs.

## Example 3: Migration Harness

Pattern: Supervisor

Workflow:

1. Parent scans target files and creates `_workspace/tasks.md`.
2. Parent batches files by dependency and risk.
3. Worker subagents process batches and write `_workspace/batches/<batch>.md`.
4. Parent applies changes, runs checks, and records unresolved blockers.

## Example 4: Deep Research Harness

Pattern: Fan-out/Fan-in

Workflow:

1. Official-source researcher writes `_workspace/01_official.md`.
2. Community researcher writes `_workspace/01_community.md`.
3. Background researcher writes `_workspace/01_background.md`.
4. Parent cross-validates claims and writes `_workspace/02_report.md`.

If web access is unavailable in the current Codex environment, record that limitation and rely only on provided/local sources.

## Example 5: Existing Claude Harness Migration

Pattern: Legacy migration

Workflow:

1. Read `CLAUDE.md`, `.claude/agents`, and `.claude/skills`.
2. Identify the legacy orchestrator and team primitives.
3. Generate equivalent `.agents/skills/<orchestrator>/SKILL.md`.
4. Convert durable agent roles to `.codex/agents/*.toml` only when useful.
5. Add a compact `AGENTS.md` pointer.
6. Preserve legacy files unless the user asks to remove them.
