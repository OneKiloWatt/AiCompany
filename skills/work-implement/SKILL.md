---
name: work-implement
description: 実装に使う。計画が揃った後、Manager が Developer へ依頼し、Tester と必要なレビュアーを調整して最終報告する。計画未確定の依頼には使わない。
---

# Work Implement

## 目的

計画が揃い、実装を開始できる状態で使う。
現行ワークフローの 4-8、つまり Developer 依頼、実装結果、検証、レビュー、差し戻し、最終報告を扱う。

## 入力

- `work-design` からの Developer 依頼
- 確定済みの目的、スコープ、非対象、受け入れ条件、制約
- 実装前レビュー結果
- 既知のリスクまたは受容済みトレードオフ

## Manager の進め方

1. `Developer` に実装依頼を送る。
2. Developer から変更要約、変更ファイル、テスト、リスクを受け取る。
3. `Tester` に検証依頼を送る。
4. `Structure Reviewer` に必ず構成レビューを依頼する。
5. 条件に応じてレビューを依頼する。
   - `Security`: セキュリティ起動条件に該当する場合。
   - `UX Reviewer`: ユーザー接点がある場合。
   - `Spec Reviewer`: 今回の実装対象に対応する `work/` 配下の仕様書、設計書がある場合、または実装がそれらで定義された振る舞いに触れる場合。
6. 指摘を分類する。
7. 実装上の問題は Developer に戻す。
8. 計画判断またはリスク受容が必要な問題は `work-consult` に戻す。
9. 必須確認が解消または明示的に受容されたら、ユーザーに最終報告する。

## 必須レビュー

- 完了前に `Tester` は必須。
- 実装後に `Structure Reviewer` は必須。
- 外部入力、認証、公開機能、秘密情報、外部通信、依存関係変更では `Security` が必須。
- GUI、CLI 文言、通知、フォームなどのユーザー接点がある場合は `UX Reviewer` が必須。
- 今回の実装対象に対応する仕様書、設計書が `work/` 配下に存在する場合、または実装がそれらで定義された振る舞いに触れる場合は `Spec Reviewer` が必須。

## 引き継ぎルール

Developer、Tester、各 Reviewer に渡す文脈は、原則として次に絞る。

- 目的
- 対象ファイルまたは成果物
- 制約
- 具体的な依頼内容
- 期待する出力形式

専門エージェントが必要としない限り、会話全体は渡さない。

## 指摘のルーティング

- Tester の失敗: Developer に戻す。
- Structure Reviewer の配置指摘: Developer に戻す。
- 実装の逸脱による Spec Reviewer の乖離指摘: Developer に戻す。
- 意図した挙動変更による Spec Reviewer の乖離指摘: User に相談し、承認後に Manager がドキュメントを更新し、Spec Reviewer に再確認を依頼する。User が却下した場合は Developer に実装修正を依頼する。
- Security または UX の計画懸念: 相談または計画に戻す。
- Security または UX の実装不備: Developer に戻す。

## やること

- Developer と Reviewer への文脈を必要最小限にする。
- 実装者と評価者の独立性を保つ。
- 指摘が修正、受容、エスカレーションのどれになったか追跡する。
- テスト結果と残リスクを含めてユーザーに最終報告する。

## やらないこと

- Tester 出力またはテスト未実施理由なしに完了扱いしない。
- Developer と評価系ロールに同じセッションを共有させない。
- ユーザー判断なしに Security または UX リスクを受容しない。
- 明示承認なしにルートのワークフロー文書を更新しない。
- 実装中に仕様、スコープ、受け入れ条件の曖昧さが見つかった場合、Developer は推測で実装せず Manager に戻す。

## 出力

```md
## 実装結果
- Developer 要約:
- 変更ファイル:

## 検証
- Tester 結果:
- 未実施テスト:

## レビュー
- Structure:
- Security:
- UX:
- Spec:

## 指摘対応
- 指摘:
- 対応: 修正済み / 受容 / エスカレーション済み

## 最終報告
- 完了:
- 残リスク:
- フォローアップ:
```

## 参照

- `references/verification-flow.md`
- `references/reviewer-request-templates.md`
