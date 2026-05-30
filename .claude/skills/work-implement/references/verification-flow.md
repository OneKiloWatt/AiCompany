# 検証フロー

## 最低限の完了確認

- Developer が変更要約、変更ファイル、テスト、リスクを返している。
- Tester が実行可能なテストを実行した、または実行不能理由を説明している。
- Structure Reviewer がフォルダ構成とファイル配置を確認している。
- 条件付きレビュアーが必要に応じて確認している。
- 指摘が修正、受容、またはエスカレーション済みになっている。

## 指摘分類

| 発生元 | 典型分類 | ルート |
| --- | --- | --- |
| Tester | 実装不備 | Developer |
| Structure Reviewer | 配置、所有境界の問題 | Developer |
| Spec Reviewer | 実装の逸脱 | Developer |
| Spec Reviewer | 意図した挙動と仕様の不一致 | work-consult |
| Security | 実装不備 | Developer |
| Security | リスク受容または計画問題 | work-consult / work-design |
| UX Reviewer | 実装不備 | Developer |
| UX Reviewer | フローやプロダクト判断 | work-consult / work-design |

## ループ上限

- Security と UX のフィードバックループは、それぞれ同一案件あたり最大 3 回。
- Tester 由来のループは、再現可能でスコープ内の失敗がある限り継続する。
- 収束できない場合は Manager が User にエスカレーションする。
