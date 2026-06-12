---
name: harness
description: "Codex-first team-architecture factory. Use when asked to build, design, audit, refactor, or maintain a harness, multi-agent workflow, Codex subagent workflow, repository skill system, AGENTS.md guidance, custom agents, or agent-team architecture for a project/domain. Also use for migrating Claude Code harnesses that mention .claude/agents, .claude/skills, CLAUDE.md, TeamCreate, SendMessage, or TaskCreate into Codex-native .agents/skills, AGENTS.md, .codex/agents, and explicit subagent orchestration."
---

# Harness - Codex Team-Architecture Factory

Build Codex-native harnesses: compact project guidance, reusable skills, optional custom agents, and explicit subagent orchestration. Treat Claude Code Agent Teams support as legacy compatibility only.

## Core Rules

1. Generate Codex artifacts by default:
   - `AGENTS.md` for persistent project guidance and harness pointer.
   - `.agents/skills/<skill-name>/SKILL.md` for orchestrator and support skills.
   - `.codex/agents/<agent-name>.toml` only when custom agent configuration is worth the overhead.
   - `_workspace/` for intermediate artifacts, audit trails, and subagent handoff files.
2. Do not generate `.claude/agents/`, `.claude/skills/`, `CLAUDE.md`, `TeamCreate`, `SendMessage`, or `TaskCreate` unless the user explicitly requests Claude legacy output.
3. Prefer simple Codex-native orchestration:
   - Use the main Codex thread as orchestrator.
   - Spawn subagents only when the task benefits from parallelism, independent review, or context isolation.
   - Use files in `_workspace/` for durable handoffs between parent and subagents.
4. Keep generated skills lean. Put detailed examples, runtime mappings, and domain-heavy material in `references/`.
5. A harness is a living system. After each meaningful run, update the harness pointer/change history and improve the relevant skill or agent instructions when feedback repeats.

## Workflow

### Phase 0: Existing Harness Audit

Before creating anything, inspect the target project:

1. Read `AGENTS.md`, `.agents/skills/`, `.codex/agents/`, and `_workspace/` if present.
2. Also detect legacy Claude artifacts: `CLAUDE.md`, `.claude/agents/`, `.claude/skills/`.
3. Classify the request:
   - **New Codex harness**: no relevant Codex harness exists.
   - **Codex harness extension**: existing Codex harness needs new skills, agents, or orchestration.
   - **Legacy migration**: Claude-style harness needs conversion to Codex-native artifacts.
   - **Maintenance audit**: user asks to inspect, synchronize, or improve an existing harness.
4. Report drift before editing: missing skills, stale pointers, duplicate agents/skills, or Claude-only runtime references.

### Phase 1: Domain Analysis

Identify the target domain, core work types, expected outputs, risk level, and technical stack. Search the codebase for existing commands, tests, conventions, and project instructions before inventing workflow rules.

Use user expertise cues to choose communication depth. Avoid unexplained jargon for non-specialists, but keep generated artifact instructions precise.

### Phase 2: Architecture Selection

Pick the smallest architecture that improves quality:

| Pattern | Codex-native implementation |
| --- | --- |
| Pipeline | Parent Codex thread runs phases in order and writes phase outputs to `_workspace/`. |
| Fan-out/Fan-in | Parent explicitly spawns parallel subagents, each writes a focused artifact, parent consolidates. |
| Expert Pool | Parent routes to only the relevant built-in/custom agent for the current request. |
| Producer-Reviewer | Worker produces artifact; reviewer subagent audits; parent applies or requests revision. |
| Supervisor | Parent maintains task state in `_workspace/tasks.md`; subagents claim or receive batches. |
| Hierarchical Delegation | Keep one level by default; use custom agents for role clarity, not nested teams. |

Read `references/agent-design-patterns.md` when choosing patterns or explaining tradeoffs.

### Phase 3: Artifact Design

Before creating new artifacts, check for reuse:

1. Existing `AGENTS.md` guidance that can receive a compact harness pointer.
2. Existing `.agents/skills/*` that can be reused or generalized.
3. Existing `.codex/agents/*.toml` whose role overlaps the requested role.

Default output layout:

```text
target-project/
├── AGENTS.md
├── .agents/
│   └── skills/
│       ├── <domain-orchestrator>/SKILL.md
│       └── <supporting-skill>/SKILL.md
├── .codex/
│   └── agents/
│       └── <optional-agent>.toml
└── _workspace/
```

### Phase 4: Skill Generation

Create one orchestrator skill plus only the support skills that remove real complexity.

Each skill must:

- Use only `name` and `description` in YAML frontmatter.
- Front-load trigger terms in `description` because Codex may shorten long skill lists.
- Put detailed procedures in the body and conditional details in `references/`.
- Include output paths and validation expectations when it orchestrates subagents.

Use `references/skill-writing-guide.md` for skill authoring details.

### Phase 5: Codex Orchestration

The orchestrator skill must specify:

1. How to prepare `_workspace/`.
2. Which subagents to spawn, if any.
3. What each subagent reads and writes.
4. How the parent consolidates results.
5. What to do on partial failure.

Use explicit Codex prompts for subagents, for example:

```text
Spawn three subagents in parallel:
1. explorer: inspect routing and API boundaries; write _workspace/01_explorer.md
2. reviewer: inspect correctness and edge cases; write _workspace/01_reviewer.md
3. tester: inspect test coverage and commands; write _workspace/01_tester.md
Wait for all results, then consolidate into _workspace/02_integrated.md.
```

Read `references/orchestrator-template.md` when writing or refactoring orchestrators.

### Phase 6: AGENTS.md Pointer

Add a compact pointer, not a full copy of the harness:

```markdown
## Harness: <domain>

**Goal:** <one-line purpose>

**Trigger:** For <domain/workflow> tasks, use the `<orchestrator-skill-name>` skill in `.agents/skills/`. Direct answers are fine for simple questions.

**Runtime:** Codex-native. Use `_workspace/` artifacts and explicit subagent orchestration. Do not use Claude-only TeamCreate/SendMessage/TaskCreate unless explicitly requested.

**Change History:**
| Date | Change | Target | Reason |
| --- | --- | --- | --- |
| YYYY-MM-DD | Initial Codex harness | all | - |
```

### Phase 7: Validation

Validate the generated harness before reporting completion:

- Skill frontmatter has only `name` and `description`.
- Generated paths are Codex-native.
- `AGENTS.md` pointer is compact and current.
- Subagent prompts have concrete inputs/outputs.
- `_workspace/` handoff paths exist or are clearly created by the workflow.
- No Claude-only primitives appear outside legacy migration notes.

For substantial harnesses, add trigger tests:

- 5 should-trigger prompts.
- 5 near-miss prompts that should not trigger.
- One dry-run scenario.
- One partial-failure scenario.

Use `references/skill-testing-guide.md` for deeper evaluation.

### Phase 8: Maintenance And Evolution

When users request harness maintenance:

1. Audit actual Codex artifacts against the orchestrator skill.
2. Fix one drift class at a time.
3. Update the `AGENTS.md` change history.
4. Re-run structural validation.
5. If feedback repeats twice, generalize the relevant skill or agent instruction.

## Reference Map

- `references/agent-design-patterns.md` - Codex-native pattern mapping and reuse rules.
- `references/orchestrator-template.md` - Templates for orchestrator skills, subagent prompts, and custom agents.
- `references/skill-writing-guide.md` - Codex skill authoring rules.
- `references/skill-testing-guide.md` - Validation and trigger testing.
- `references/qa-agent-guide.md` - QA methodology for generated build/review harnesses.
- `references/team-examples.md` - Example Codex harnesses.
- `references/claude-legacy.md` - Claude Code migration notes.
