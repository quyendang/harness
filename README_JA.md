# Harness — Codex チームアーキテクチャファクトリー

[English](README.md) | [한국어](README_KO.md) | **日本語**

> この日本語 README は要約版です。最新のインストール手順と開発情報は [README.md](README.md) を基準にしてください。

Harness は、プロジェクトやドメインの説明を Codex ネイティブなワークフローに変換します:

- `AGENTS.md` のプロジェクト指示
- `.agents/skills/` の再利用可能な Codex スキル
- 必要に応じた `.codex/agents/` カスタムエージェント
- `_workspace/` による成果物の受け渡し
- 明示的な Codex subagent オーケストレーション

既存の Claude Code Agent Teams パッケージ (`.claude-plugin/`) はレガシー互換のために残しています。新規利用では `.codex-plugin/plugin.json` と Codex ランタイムを使ってください。

## Quick Start

```bash
codex plugin marketplace add quyendang/harness
codex plugin install harness
```

またはリポジトリローカルのスキルとして使います:

```bash
mkdir -p .agents/skills
cp -R skills/harness .agents/skills/harness
```

Codex で:

```text
Use $harness to build a Codex workflow for this repo.
```

## Claude Harness からの移行

| Claude | Codex |
| --- | --- |
| `CLAUDE.md` | `AGENTS.md` |
| `.claude/skills` | `.agents/skills` |
| `.claude/agents/*.md` | 必要な場合のみ `.codex/agents/*.toml` |
| `TeamCreate` / `SendMessage` / `TaskCreate` | 明示的な subagent + `_workspace/` ファイル |

詳細は [docs/codex-migration.md](docs/codex-migration.md) を参照してください。
