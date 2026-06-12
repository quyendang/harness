# Codex Agent Workflow Design Patterns

Use these patterns when Harness designs repository skills, custom agents, and explicit subagent workflows for Codex.

## Runtime Model

Codex does not need Claude Code Agent Teams primitives. Use this mapping:

| Concept | Codex-native mechanism |
| --- | --- |
| Persistent project guidance | `AGENTS.md` |
| Reusable workflow | `.agents/skills/<name>/SKILL.md` |
| Role-specific spawned worker | Built-in subagent or `.codex/agents/<name>.toml` custom agent |
| Team state | `_workspace/tasks.md` and phase artifacts |
| Inter-agent handoff | Files in `_workspace/`, consolidated by the parent Codex thread |
| Validation | Tests, dry-runs, trigger checks, and reviewer subagents |

Codex subagents are explicit: the user or orchestrator prompt must ask Codex to spawn them. Avoid implying hidden autonomous team communication.

## Execution Modes

### Single Parent Thread

Use when the work is linear, small, or mostly requires editing one area. This is the cheapest and easiest mode.

### Parallel Subagents

Use when the work benefits from independent context windows or parallel review. Good examples: codebase exploration, multi-perspective review, independent research, and test coverage audits.

Rules:

- Give each subagent one focused role.
- Specify exact read targets and output file path.
- Ask the parent to wait for all results before synthesis.
- Keep final decision-making in the parent thread.

### Custom Agents

Use `.codex/agents/<name>.toml` when a role needs persistent instructions, sandbox defaults, or model/config overrides across multiple workflows.

Use custom agents sparingly. For one-off roles, a clear subagent prompt is enough.

## Architecture Patterns

### 1. Pipeline

Sequential phases where each output feeds the next.

```text
analyze -> design -> implement -> review
```

Use the parent thread as the orchestrator. Write each phase output to `_workspace/NN_phase.md`. Spawn a subagent only for a phase that needs independent context.

### 2. Fan-out/Fan-in

Parallel independent work followed by synthesis.

```text
parent -> explorer
       -> reviewer
       -> tester
parent <- artifacts -> integrated report
```

This is the strongest Codex subagent pattern. Each subagent writes one artifact; the parent compares, resolves conflicts, and produces the final result.

### 3. Expert Pool

The parent chooses one or more experts based on the request.

Use for broad domains where every request does not need every specialist. Prefer routing in the orchestrator skill over creating many always-on agents.

### 4. Producer-Reviewer

One role produces; another role validates.

Use for code generation, docs, migration plans, and high-risk edits. The reviewer should inspect the artifact and the source context, then write actionable findings. The parent applies revisions and records unresolved issues.

### 5. Supervisor

The parent maintains task state and assigns batches.

Use `_workspace/tasks.md` as the source of truth. Include task owner, status, input paths, output paths, and blockers. Subagents do not own global state.

### 6. Hierarchical Delegation

Break a large goal into subgoals, but avoid deep nesting.

Codex custom agents are configuration layers for spawned sessions, not a full team hierarchy. Keep delegation one level deep unless the user explicitly accepts cost and complexity.

## Agent/Skill Reuse

Before creating a new artifact, check existing `AGENTS.md`, `.agents/skills`, and `.codex/agents`.

| Situation | Action |
| --- | --- |
| Existing skill fully covers the need | Reuse it; update orchestrator links only. |
| Existing skill partially overlaps and can generalize | Extend it and update its description boundaries. |
| Role is domain-specific by design | Create a new skill or custom agent. |
| Role is one-off | Use a subagent prompt, not a persistent custom agent. |

## Skill vs Custom Agent

| Use a skill when | Use a custom agent when |
| --- | --- |
| The reusable value is a workflow or procedure. | The reusable value is a role/persona/configuration. |
| Codex should trigger it from task wording. | The parent explicitly spawns it. |
| It needs references, scripts, or assets. | It needs persistent developer instructions or sandbox defaults. |

Most harnesses should have more skills than custom agents.

## Custom Agent TOML Shape

Use this minimal shape when a custom agent is warranted:

```toml
name = "qa-reviewer"
description = "Reviews implementation artifacts for correctness, integration gaps, and missing tests."
developer_instructions = """
Focus on defects and evidence. Read both producer and consumer boundaries before reporting integration issues.
Write findings to the output path requested by the parent Codex thread.
"""
```

Do not invent unsupported fields unless the target Codex configuration already uses them.
