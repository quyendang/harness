# Codex QA Agent Guide

Use this when a generated harness includes QA or review roles.

## Purpose

QA subagents should find boundary mismatches, missing tests, runtime risks, and integration drift. They should not merely confirm that files exist.

## Recommended Codex Shape

Use a plain subagent prompt for one-off QA. Create `.codex/agents/qa-reviewer.toml` only when the role will be reused across workflows.

Minimal custom agent:

```toml
name = "qa-reviewer"
description = "Reviews implementation artifacts for integration mismatches, missing tests, and runtime risks."
developer_instructions = """
Prioritize boundary checks over existence checks.
Read both producer and consumer sides before reporting integration issues.
Write findings with evidence paths and concrete remediation.
"""
```

## Verification Priorities

1. Integration coherence: producer and consumer contracts match.
2. Runtime behavior: async/sync expectations, nullability, error paths.
3. Test coverage: critical flows and regressions are covered.
4. Project conventions: generated code follows existing patterns.
5. UX/design quality when relevant.

## Boundary Checks

| Boundary | Compare |
| --- | --- |
| API response to frontend hook | Response shape vs expected type and unwrap logic |
| Route files to links | Actual page paths vs `href`, `router.push`, redirects |
| State machine to updates | Allowed transitions vs code that changes status |
| DB to API to UI | Field names, casing, nullability, optional fields |
| Immediate response to background result | 202/queued payload vs final result payload |

## QA Prompt Template

```text
Spawn a QA subagent.
Input:
- Implementation artifact: _workspace/02_integrated.md
- Source areas: <paths>
Task:
- Check integration boundaries by reading both sides of each contract.
- Identify missing tests and likely runtime failures.
- Write _workspace/03_qa_review.md with severity, evidence, and suggested fixes.
```

## Reporting Format

```markdown
## Findings

### High: <title>
- Evidence: <file/path or artifact>
- Boundary: <producer> -> <consumer>
- Impact: <runtime/user impact>
- Suggested fix: <specific action>

## Not Verified

- <area skipped and why>
```

## Timing

Run QA incrementally after each major module or artifact, not only at the end. Early boundary failures are cheaper to fix.
