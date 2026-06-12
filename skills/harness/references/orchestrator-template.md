# Codex Orchestrator Templates

Use these templates when Harness generates `.agents/skills/<domain-orchestrator>/SKILL.md`.

## Template A: Parallel Subagent Fan-out/Fan-in

```markdown
---
name: <domain>-orchestrator
description: "Codex orchestrator for <domain>. Use for <primary triggers>, multi-agent review, reruns, updates, partial refreshes, and improving prior <domain> results."
---

# <Domain> Orchestrator

Coordinate <domain> work with explicit Codex subagents and `_workspace/` artifacts.

## Workflow

### Phase 0: Context Check

1. Inspect `AGENTS.md`, `.agents/skills/`, `.codex/agents/`, and `_workspace/`.
2. If `_workspace/` exists and the user asks for a partial update, reuse existing artifacts and rerun only affected roles.
3. If the user provides a new input, archive prior `_workspace/` to `_workspace_YYYYMMDD_HHMMSS/`.

### Phase 1: Prepare

1. Create `_workspace/` if missing.
2. Save normalized input to `_workspace/00_input.md`.
3. Create or update `_workspace/tasks.md`.

### Phase 2: Spawn Subagents

Ask Codex to spawn subagents in parallel:

1. `<role-a>`: read `<inputs>` and write `_workspace/01_<role-a>.md`.
2. `<role-b>`: read `<inputs>` and write `_workspace/01_<role-b>.md`.
3. `<role-c>`: read `<inputs>` and write `_workspace/01_<role-c>.md`.

Wait for all results before continuing.

### Phase 3: Integrate

1. Read every `_workspace/01_*.md` artifact.
2. Resolve conflicts by preserving source attribution.
3. Write `_workspace/02_integrated.md`.
4. Produce the final user-facing output.

### Phase 4: Validate

Run the project checks discovered in `AGENTS.md`, package scripts, or repo docs. If checks cannot run, record why.

## Failure Handling

- One subagent fails: retry once if the failure is transient; otherwise continue and mark the missing area.
- Multiple subagents fail: stop and ask the user whether to continue with partial evidence.
- Conflicting findings: keep both, cite artifact sources, and make the parent decision explicit.
```

## Template B: Producer-Reviewer

```markdown
---
name: <domain>-producer-reviewer
description: "Codex producer-reviewer workflow for <domain>. Use when output quality, correctness, or integration safety needs independent review."
---

# <Domain> Producer-Reviewer

## Workflow

1. Parent prepares `_workspace/00_input.md`.
2. Spawn producer subagent to create `_workspace/01_draft.md`.
3. Spawn reviewer subagent to inspect the draft and source context, writing `_workspace/02_review.md`.
4. Parent applies accepted reviewer feedback and writes `_workspace/03_final.md`.
5. Parent reports unresolved reviewer concerns separately.
```

## Template C: Supervisor With Task File

```markdown
---
name: <domain>-supervisor
description: "Codex supervisor workflow for <domain>. Use for large batch work, migrations, multi-file refactors, and dynamically assigned review or implementation tasks."
---

# <Domain> Supervisor

## Workflow

1. Parent enumerates work into `_workspace/tasks.md`.
2. Parent batches tasks by risk, dependency, and file ownership.
3. Spawn worker subagents with explicit batch assignments.
4. Each worker writes `_workspace/batches/<batch-id>.md`.
5. Parent integrates results, runs checks, and updates task statuses.
```

## AGENTS.md Pointer Template

```markdown
## Harness: <domain>

**Goal:** <one-line purpose>

**Trigger:** For <domain/workflow> tasks, use `<orchestrator-skill>` in `.agents/skills/`. Answer directly for simple questions.

**Runtime:** Codex-native. Use `_workspace/` artifacts and explicit subagent orchestration. Do not use Claude-only TeamCreate/SendMessage/TaskCreate unless explicitly requested.

**Change History:**
| Date | Change | Target | Reason |
| --- | --- | --- | --- |
| YYYY-MM-DD | Initial Codex harness | all | - |
```

## Custom Agent Template

```toml
name = "<agent-name>"
description = "<short role summary and when the parent should spawn this agent>"
developer_instructions = """
You are the <role> for this harness.
Read the input paths provided by the parent Codex thread.
Write your result to the exact output path requested by the parent.
Report blockers and uncertainty explicitly.
"""
```

## Writing Rules

- Do not mention `TeamCreate`, `SendMessage`, or `TaskCreate` in Codex orchestrators.
- Do not require custom agents when a clear subagent prompt is enough.
- Always specify handoff file paths.
- Keep parent thread responsible for synthesis and final edits.
- Include one normal dry-run and one partial-failure scenario in substantial orchestrator skills.
