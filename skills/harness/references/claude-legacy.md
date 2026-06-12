# Claude Code Legacy Migration

Use this only when a project already contains a Claude Code harness or the user explicitly asks for Claude compatibility.

## Legacy To Codex Mapping

| Claude artifact or primitive | Codex replacement |
| --- | --- |
| `CLAUDE.md` | `AGENTS.md` |
| `.claude/skills/<name>/SKILL.md` | `.agents/skills/<name>/SKILL.md` |
| `.claude/agents/<name>.md` | `.codex/agents/<name>.toml` only when persistent custom configuration is useful |
| `TeamCreate` | Explicit request to spawn subagents in parallel |
| `SendMessage` | Parent-managed file handoff through `_workspace/` |
| `TaskCreate` / `TaskUpdate` | `_workspace/tasks.md` |

## Migration Steps

1. Audit legacy files and identify the orchestrator skill.
2. Convert the orchestrator workflow from team primitives to explicit Codex subagent instructions.
3. Move reusable skills to `.agents/skills/`.
4. Convert only durable roles into `.codex/agents/*.toml`; leave one-off roles as subagent prompts.
5. Replace the `CLAUDE.md` block with a compact `AGENTS.md` harness pointer.
6. Run structural validation and search for leftover Claude-only primitives.

## Preserve When Needed

If the repo must continue supporting Claude Code, keep the original `.claude/` files and mark them as legacy. Do not let Codex orchestrators depend on those files.
