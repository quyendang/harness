# Codex Skill Writing Guide

Use this when Harness creates or refactors `.agents/skills/<name>/SKILL.md`.

## Description

The description is the primary trigger surface. Codex may shorten long skill lists, so put the highest-value trigger words first.

Good pattern:

```yaml
description: "Codex API review workflow. Use for reviewing route handlers, client hooks, runtime data shapes, integration boundaries, missing tests, or rerunning prior API review artifacts."
```

Rules:

- Say what the skill does.
- Say when it should trigger.
- Include important near-boundaries when needed.
- Do not put trigger rules only in the body; Codex reads the body after choosing the skill.

## Body

Write direct procedural instructions for another Codex instance.

Prefer:

- Short workflow phases.
- Concrete file paths.
- Clear inputs and outputs.
- Failure handling that preserves partial results.
- References for domain-heavy details.

Avoid:

- README-like marketing copy.
- Long explanations of concepts Codex already knows.
- Claude-only terms unless the skill is explicitly a legacy migration skill.
- Extra frontmatter fields beyond `name` and `description`.

## Codex Artifact Paths

Use these defaults:

```text
AGENTS.md
.agents/skills/<skill-name>/SKILL.md
.agents/skills/<skill-name>/references/<topic>.md
.agents/skills/<skill-name>/scripts/<script>
.codex/agents/<agent-name>.toml
_workspace/<phase>_<role>_<artifact>.md
```

Do not generate `.claude/` paths for Codex harnesses.

## Progressive Disclosure

Keep the main `SKILL.md` focused on the workflow. Move details into `references/` when:

- The detail is only needed for one runtime, framework, or domain.
- The file approaches 500 lines.
- Examples are useful but not always needed.
- A long schema or checklist would crowd the core instructions.

Reference files should be linked directly from `SKILL.md`; avoid deep nesting.

## Scripts

Add scripts only when deterministic execution matters or agents repeatedly write the same helper code. Test every added script at least once.

## Data Contracts

For multi-agent workflows, define artifact contracts:

```markdown
## Output Contract

Write `_workspace/01_security_review.md` with:

- Scope reviewed
- Findings with severity
- Evidence paths
- Uncertainty or skipped areas
```

The parent thread should be able to integrate artifacts without guessing their structure.

## Reuse Rules

Before creating a new skill:

| Situation | Action |
| --- | --- |
| Existing skill fully covers the workflow | Reuse it. |
| Existing skill partly overlaps | Generalize it if that will not blur its purpose. |
| Workflow is intentionally domain-specific | Create a new skill. |
| Role is only a persona | Prefer a custom agent or subagent prompt. |

Update descriptions when expanding scope.
