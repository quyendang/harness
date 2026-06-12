# Codex Skill Testing Guide

Use this when validating generated Harness skills.

## Structural Checks

- `SKILL.md` exists.
- Frontmatter contains only `name` and `description`.
- Skill name is lower-case hyphen-case.
- Description front-loads trigger terms.
- References linked from `SKILL.md` exist.
- Codex harnesses use `AGENTS.md`, `.agents/skills`, `.codex/agents`, and `_workspace/`.
- Claude-only primitives appear only in legacy migration docs.

## Trigger Tests

Write at least 10 trigger checks for important skills:

- 5 should-trigger prompts.
- 5 should-not-trigger near misses.

Good should-trigger examples:

```text
Build a Codex harness for this repo.
Create a multi-agent review workflow for our API layer.
Migrate this Claude TeamCreate harness to Codex.
```

Good near misses:

```text
Explain what this repository does.
Run the test suite.
Review this single function without changing project workflow.
```

Near misses should be plausible; avoid obviously unrelated prompts.

## Dry-Run Validation

For an orchestrator skill, simulate one run without making project changes:

1. Identify input.
2. List planned subagents.
3. Confirm each subagent has input paths and output paths.
4. Confirm parent consolidation step.
5. Confirm test/check commands.
6. Confirm partial-failure behavior.

## With-Skill vs Baseline

For high-value skills, compare:

- **With skill**: ask Codex to use the generated skill.
- **Baseline**: ask the same task without the skill.

Evaluate:

- Completeness.
- Correct use of repo conventions.
- Artifact clarity.
- Fewer missed edge cases.
- Cost or complexity increase.

## Workspace Layout

Use a repeatable evaluation layout:

```text
_workspace/evals/
├── eval-harness-generation/
│   ├── input.md
│   ├── with_skill.md
│   ├── baseline.md
│   └── grading.md
└── trigger-tests.md
```

Do not overwrite previous iterations when comparing skill changes.

## Grading Format

Use compact assertions:

```json
{
  "expectations": [
    {
      "text": "Generated artifacts use .agents/skills instead of .claude/skills",
      "passed": true,
      "evidence": "Dry-run output lists .agents/skills/domain-orchestrator/SKILL.md"
    }
  ],
  "summary": {
    "passed": 1,
    "failed": 0,
    "total": 1,
    "pass_rate": 1.0
  }
}
```

## Iteration

When a test fails:

1. Generalize the correction.
2. Update the smallest relevant skill or reference.
3. Re-run the structural check and the failing scenario.
4. Record meaningful behavior changes in the harness change history.
