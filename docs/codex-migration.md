# Migrating Claude Harnesses To Codex

Use this when a project already contains `CLAUDE.md`, `.claude/agents`, or `.claude/skills`.

## Mapping

| Claude Code | Codex |
| --- | --- |
| `CLAUDE.md` | `AGENTS.md` |
| `.claude/skills/<name>/SKILL.md` | `.agents/skills/<name>/SKILL.md` |
| `.claude/agents/<name>.md` | `.codex/agents/<name>.toml` only for durable custom roles |
| `TeamCreate` | Explicit Codex request to spawn subagents |
| `SendMessage` | `_workspace/` artifact handoff |
| `TaskCreate` / `TaskUpdate` | `_workspace/tasks.md` |

## Steps

1. Audit existing Claude artifacts and find the orchestrator skill.
2. Rewrite the orchestrator as a Codex skill under `.agents/skills/`.
3. Convert team-member roles into subagent prompts; create `.codex/agents/*.toml` only when the role is reused.
4. Add a compact `AGENTS.md` pointer with trigger, runtime, and change history.
5. Preserve legacy Claude files unless the user explicitly asks to remove them.
6. Search the Codex workflow for `TeamCreate`, `SendMessage`, `TaskCreate`, `.claude/`, and `CLAUDE.md`; these should appear only in migration notes.

## Validation

Run:

```bash
find .agents/skills -maxdepth 3 -type f
find .codex/agents -maxdepth 2 -type f 2>/dev/null || true
rg "TeamCreate|SendMessage|TaskCreate|\\.claude/|CLAUDE.md" AGENTS.md .agents .codex
```

The final `rg` command should return no matches unless the generated harness intentionally includes legacy migration guidance.
