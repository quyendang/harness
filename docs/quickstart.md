# Quickstart — Build Your First Codex Harness

This guide installs Harness as a Codex plugin or repo-local skill, then uses it to generate a Codex-native multi-agent workflow.

## Prerequisites

- Codex CLI or Codex app with plugin and skill support.
- A Git repository you want Codex to work in.
- Permission to write repo files such as `AGENTS.md`, `.agents/skills/`, and `_workspace/`.

## Option A — Install As A Plugin

```bash
codex plugin marketplace add quyendang/harness
codex plugin add harness --marketplace harness-marketplace
```

Then run Codex from your target repo and ask:

```text
Use $harness to build a Codex workflow for this repo.
```

## Option B — Use Repo-Local Skill

From this repository:

```bash
mkdir -p <target-repo>/.agents/skills
cp -R skills/harness <target-repo>/.agents/skills/harness
```

Then start Codex in `<target-repo>` and ask:

```text
Use $harness to design a multi-agent code review workflow.
```

## Expected Output

Harness should plan or create:

```text
AGENTS.md
.agents/skills/<domain-orchestrator>/SKILL.md
.agents/skills/<supporting-skill>/SKILL.md
.codex/agents/<optional-agent>.toml
_workspace/
```

## Verify

Check the generated harness:

```bash
find .agents/skills -maxdepth 3 -type f
find .codex/agents -maxdepth 2 -type f 2>/dev/null || true
test -f AGENTS.md && sed -n '1,160p' AGENTS.md
```

The generated workflow should mention Codex subagents and `_workspace/` artifacts. It should not require Claude-only `TeamCreate`, `SendMessage`, or `TaskCreate`.

## Legacy Claude Users

The old `.claude-plugin/` package remains for compatibility. New work should use the Codex plugin manifest in `.codex-plugin/`.
