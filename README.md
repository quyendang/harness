<p align="center">
  <img src="harness_banner.png" alt="Harness Banner" width="600">
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Version-1.3.0-brightgreen.svg" alt="Version">
  <a href="LICENSE"><img src="https://img.shields.io/badge/License-Apache_2.0-blue.svg" alt="License"></a>
  <img src="https://img.shields.io/badge/Codex-Plugin-purple.svg" alt="Codex Plugin">
  <img src="https://img.shields.io/badge/Patterns-6_Architectures-orange.svg" alt="6 Architecture Patterns">
  <img src="https://img.shields.io/badge/Runtime-Subagents-green.svg" alt="Codex Subagents">
</p>

# Harness — Codex Team-Architecture Factory

**English** | [한국어](README_KO.md) | [日本語](README_JA.md)

Harness turns a domain or repository brief into a Codex-native agent workflow: `AGENTS.md` guidance, reusable `.agents/skills`, optional `.codex/agents`, and explicit subagent orchestration.

The original Claude Code Agent Teams packaging remains in `.claude-plugin/` for legacy users, but Codex is now the primary runtime.

## What Harness Generates

```text
your-project/
├── AGENTS.md
├── .agents/
│   └── skills/
│       ├── <domain-orchestrator>/SKILL.md
│       └── <supporting-skill>/SKILL.md
├── .codex/
│   └── agents/
│       └── <optional-agent>.toml
└── _workspace/
    ├── 00_input.md
    ├── 01_<role>.md
    └── 02_integrated.md
```

## Key Features

- **Codex-native packaging** — installable plugin manifest at `.codex-plugin/plugin.json`.
- **Repository skills** — generated workflows live in `.agents/skills/` so Codex can discover them from the repo.
- **Project guidance** — compact `AGENTS.md` pointers keep future Codex runs aligned without bloating context.
- **Subagent orchestration** — fan-out/fan-in, producer-reviewer, supervisor, pipeline, expert-pool, and hierarchical delegation patterns expressed with explicit Codex subagent prompts and `_workspace/` artifacts.
- **Legacy migration** — converts Claude-style `.claude/agents`, `.claude/skills`, `CLAUDE.md`, `TeamCreate`, `SendMessage`, and `TaskCreate` patterns into Codex-native equivalents.

## Quick Start

### Install From A Marketplace

```shell
codex plugin marketplace add quyendang/harness
codex plugin install harness
```

### Use As A Repo Skill During Development

```shell
mkdir -p .agents/skills
cp -R skills/harness .agents/skills/harness
```

Then ask Codex:

```text
Use $harness to build a Codex workflow for this repo.
```

## Architecture Patterns

| Pattern | Codex implementation |
| --- | --- |
| Pipeline | Parent thread runs phases in order through `_workspace/` artifacts. |
| Fan-out/Fan-in | Parent spawns focused subagents in parallel and consolidates their artifacts. |
| Expert Pool | Parent routes to only the relevant specialist prompt or custom agent. |
| Producer-Reviewer | Worker produces, reviewer audits, parent applies revisions. |
| Supervisor | Parent owns `_workspace/tasks.md` and assigns batches to subagents. |
| Hierarchical Delegation | One-level decomposition by default; avoid nested teams unless explicitly needed. |

## Plugin Structure

```text
harness/
├── .codex-plugin/
│   └── plugin.json
├── .claude-plugin/              # Legacy Claude Code packaging
├── assets/
├── skills/
│   └── harness/
│       ├── SKILL.md
│       ├── agents/openai.yaml
│       └── references/
└── docs/
```

## Use Cases

```text
Use $harness to create a code review workflow with security, correctness, and test subagents.
```

```text
Use $harness to migrate this Claude TeamCreate harness to Codex-native skills and AGENTS.md.
```

```text
Use $harness to design a documentation generator for this API codebase.
```

## Migration From Claude Harness

Harness maps legacy Claude artifacts to Codex equivalents:

| Claude | Codex |
| --- | --- |
| `CLAUDE.md` | `AGENTS.md` |
| `.claude/skills` | `.agents/skills` |
| `.claude/agents/*.md` | `.codex/agents/*.toml` when persistent custom agents are useful |
| `TeamCreate` | Explicit request to spawn subagents |
| `SendMessage` | `_workspace/` file handoff |
| `TaskCreate` | `_workspace/tasks.md` |

See [docs/codex-migration.md](docs/codex-migration.md).

## Development

Useful validation commands:

```shell
python3 -m json.tool .codex-plugin/plugin.json
python3 -m json.tool .claude-plugin/plugin.json
python3 /Users/quyen.eth/.codex/skills/.system/skill-creator/scripts/quick_validate.py skills/harness
```

## License

Apache-2.0. See [LICENSE](LICENSE).
