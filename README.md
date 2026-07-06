# AiCompany

Claude Code でマルチエージェント開発を行うためのワークフロー定義リポジトリです。
エージェントの役割・権限・工程を定義し、複数の Claude Code エージェントが協調して開発を進める仕組みを提供します。

> **エージェントの方へ:** セッション開始時は `CLAUDE.md` を参照してください。

## はじめ方

1. このリポジトリをクローンまたはテンプレートとして利用する
2. プロジェクトルートで `claude` を起動する（`CLAUDE.md` が自動的に読み込まれます）
3. `MULTI_AGENT_WORKFLOW.md` でワークフロー全体の構成を確認する
4. 実装成果物は `work/` 配下に置く（開発開始時に作成）

## 構成

```
AiCompany/
├── .claude/                     # Claude Code 設定・エージェント定義
│   ├── agents/                  # 各エージェントの定義（9 エージェント）
│   │   ├── MANAGER.md
│   │   ├── DEVELOPER.md
│   │   ├── TESTER.md
│   │   └── ...
│   └── skills/                  # 工程別の作業手順（4 工程）
│       ├── work-investigate/    # 調査
│       ├── work-consult/        # 相談
│       ├── work-design/         # 設計
│       └── work-implement/      # 実装
├── CLAUDE.md                    # Claude Code プロジェクト指示書（自動読み込み）
├── MULTI_AGENT_WORKFLOW.md      # ワークフロー全体定義
└── work/                        # 実装成果物（Developer が管理・開発開始時に作成）
```

> `.claude/` はドットフォルダです。ローカルのターミナルで確認する際は `ls -a` を使用してください。

## 工程フロー

| 工程 | 概要 | Skill |
|---|---|---|
| 調査 | 事実・影響範囲・不明点を把握する | [work-investigate](./.claude/skills/work-investigate/) |
| 相談 | ユーザーと仕様・方針・優先度を決める | [work-consult](./.claude/skills/work-consult/) |
| 設計 | 目的・スコープ・受け入れ条件を確定する | [work-design](./.claude/skills/work-design/) |
| 実装 | Developer に依頼し、テスト・レビューまで完了させる | [work-implement](./.claude/skills/work-implement/) |

## エージェント構成

| ロール | 主な責務 | 権限 |
|---|---|---|
| Manager | ユーザー窓口・工程管理・最終判断 | Spec Docs 読み書き |
| Developer | 実装・テスト・コード付随ドキュメント | `work/` 配下を読み書き |
| Tester | 実装の検証・テスト実行 | テストコード読み書き |
| Security | セキュリティリスクの確認・報告 | 全ファイル読み取り専用 |
| UX Reviewer | ユーザー接点の品質確認・報告 | 全ファイル読み取り専用 |
| Structure Reviewer | フォルダ構成・配置の確認・報告 | 全ファイル読み取り専用 |
| Spec Reviewer | 仕様と実装の整合性確認・報告 | 全ファイル読み取り専用 |
| Consistency Reviewer | 既存コードとの整合性・影響範囲の確認・報告 | 全ファイル読み取り専用 |
| Supervisor | 外部エージェント（Codex 等）利用時の Manager 監理 | Manager 相当 |

## 関連ドキュメント

- [MULTI_AGENT_WORKFLOW.md](./MULTI_AGENT_WORKFLOW.md) — ワークフロー全体の詳細定義
- [CLAUDE.md](./CLAUDE.md) — Claude Code プロジェクト指示書
- [.claude/agents/](./.claude/agents/) — 各ロールの詳細定義
- [.claude/skills/](./.claude/skills/) — 各工程の作業手順
