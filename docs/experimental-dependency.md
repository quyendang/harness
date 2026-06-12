# Runtime Dependency Notes

Harness is now Codex-first.

## Codex Runtime

Codex harnesses use:

- `AGENTS.md` for project instructions.
- `.agents/skills/` for reusable repository skills.
- `.codex/agents/` for optional custom agents.
- Explicit subagent prompts for parallel work.
- `_workspace/` files for durable handoff and audit trails.

Codex subagent workflows are enabled in current Codex releases and do not require the legacy Claude Code `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1` flag.

## Legacy Claude Runtime

Older Harness versions targeted Claude Code Agent Teams and used:

- `CLAUDE.md`
- `.claude/agents/`
- `.claude/skills/`
- `TeamCreate`
- `SendMessage`
- `TaskCreate`
- `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1`

Those concepts remain documented only for migration and backward compatibility. New harnesses should not depend on them.

## Migration

Use [docs/codex-migration.md](codex-migration.md) to convert Claude harnesses into Codex-native artifacts.
