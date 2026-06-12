# Harness — Codex 팀 아키텍처 팩토리

[English](README.md) | **한국어** | [日本語](README_JA.md)

> 이 한국어 README는 요약본입니다. 최신 설치/개발 정보의 기준 문서는 [README.md](README.md)입니다.

Harness는 프로젝트나 도메인 설명을 Codex 네이티브 워크플로우로 변환합니다:

- `AGENTS.md` 프로젝트 지침
- `.agents/skills/` 재사용 가능한 Codex 스킬
- 필요 시 `.codex/agents/` 커스텀 에이전트
- `_workspace/` 기반 산출물 전달
- 명시적인 Codex subagent 오케스트레이션

기존 Claude Code Agent Teams 패키징(`.claude-plugin/`)은 레거시 호환용으로 유지됩니다. 신규 작업은 `.codex-plugin/plugin.json`과 Codex 런타임을 기준으로 합니다.

## 빠른 시작

```bash
codex plugin marketplace add revfactory/harness
codex plugin install harness
```

또는 저장소 로컬 스킬로 사용:

```bash
mkdir -p .agents/skills
cp -R skills/harness .agents/skills/harness
```

Codex에서:

```text
Use $harness to build a Codex workflow for this repo.
```

## Claude Harness 마이그레이션

| Claude | Codex |
| --- | --- |
| `CLAUDE.md` | `AGENTS.md` |
| `.claude/skills` | `.agents/skills` |
| `.claude/agents/*.md` | 필요 시 `.codex/agents/*.toml` |
| `TeamCreate` / `SendMessage` / `TaskCreate` | 명시적 subagent + `_workspace/` 파일 |

자세한 내용은 [docs/codex-migration.md](docs/codex-migration.md)를 참고하세요.
