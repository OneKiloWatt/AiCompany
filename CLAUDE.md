# CLAUDE.md

このプロジェクトはマルチエージェントで開発します。
作業前に必ず `MULTI_AGENT_WORKFLOW.md` を読み、自分の役割を確認してください。
役割の詳細は `roles/` 配下の該当ファイルを参照してください。
フォルダ構成・保守性の確認は `roles/STRUCTURE_REVIEWER.md` を参照してください。

## スキルの自動適用

ユーザーの依頼内容から工程を判断し、作業を始める前に対応するスキルの `SKILL.md` を読むこと。

| 判断する工程 | 読むファイル |
| --- | --- |
| 調査を依頼された、または事実・影響範囲を把握する必要がある | `skills/work-investigate/SKILL.md` |
| 相談・仕様決め・方針確認を求められた | `skills/work-consult/SKILL.md` |
| 設計・要件整理・実装前の計画を依頼された | `skills/work-design/SKILL.md` |
| 実装・コード変更・機能追加を依頼された | `skills/work-implement/SKILL.md` |

複数の工程にまたがる場合は、該当するすべての `SKILL.md` を読む。
