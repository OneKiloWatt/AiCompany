# Codex Multi-Agent Workflow

## 目的

このドキュメントは、複数エージェントで開発するときの共通ルールを定義する。

役割ごとの詳細は次を参照する。

- `roles/MANAGER.md`
- `roles/DEVELOPER.md`
- `roles/SECURITY.md`
- `roles/TESTER.md`
- `roles/UX_REVIEWER.md`
- `roles/STRUCTURE_REVIEWER.md`

## 基本方針

- ユーザーは `Manager` と仕様を固める
- 仕様が固まるまで `Developer` は実装しない
- 要件確定後、`Manager` は自分で実装せず `Developer` を起動する
- 実装コードを変更できるのは `Developer` のみ
- 仕様書・設計書・ワークフロー文書を更新できるのは `Manager` のみ
- コードに付随するドキュメント(コメント、docstring、コード配下の README など)は `Developer` が更新できる
- テストコード・フィクスチャ・スナップショットは `Developer` と `Tester` が更新できる
- `Security`・`UX Reviewer`・`Structure Reviewer` は原則 read-only で確認と報告を行う
- 完了前に `Tester` の確認を必須とする

## 成果物の配置ルール

- `Developer` が作成・変更する実装コードはすべて `work/` 配下に置く
- `Developer` は `work/` の外を変更しない(ワークフロー文書と役割定義を保護するため)
- `work/` 内のフォルダ構成は案件に応じて `Developer` が整える(言語や構成に応じて柔軟に決めてよい)
- ワークフロー文書(`MULTI_AGENT_WORKFLOW.md`)と役割定義(`roles/`)は `work/` の外に置き、`Manager` のみが更新する

## 権限マトリクス

| Role | Spec Docs | Code Docs | Prod Code | Test Code | Test Execution | Spawn Sub-agents |
| --- | --- | --- | --- | --- | --- | --- |
| Manager | Read / Write | Read Only | Read Only | Read Only | Optional | Yes |
| Developer | Read Only | Read / Write | Read / Write | Read / Write | Yes | Yes |
| Security | Read Only | Read Only | Read Only | Read Only | Optional | No |
| Tester | Read Only | Read Only | Read Only | Read / Write | Yes | No |
| UX Reviewer | Read Only | Read Only | Read Only | Read Only | Optional | No |
| Structure Reviewer | Read Only | Read Only | Read Only | Read Only | No | No |

定義:

- **Spec Docs**: 仕様書、設計書、ワークフロー文書(本ファイルおよび `roles/` 配下)
- **Code Docs**: コード内コメント、docstring、コードに付随する README など、コード成果物に紐づくドキュメント
- **Prod Code**: 製品として動作するアプリケーション・ライブラリのコード
- **Test Code**: 自動テスト、フィクスチャ、スナップショット、テスト用ユーティリティ

## 進め方

1. User -> Manager
仕様、制約、受け入れ条件を固める。

2. Manager -> Security / UX Reviewer / Developer
実装前にそれぞれの担当者に対して、それぞれの観点で設計レビューを依頼する。
UX Reviewer はユーザーが触る画面・操作を含む案件でのみ起動する。

3. Security / UX Reviewer -> Manager
各担当からのフィードバックを確認し、設計書を修正する。
判断がつかないものは User に確認する。
修正後は再度 2 に戻る。
問題がなければ、次に進む。

4. Manager -> Developer
実装依頼を出す。スコープと完了条件を明記する。

5. Developer -> Manager
実装結果、影響範囲、未解決事項を返す。

6. Manager -> Tester / Security / UX Reviewer / Structure Reviewer
テストとレビューを依頼する。
UX Reviewer はユーザーが触る画面・操作を含む案件でのみ起動する。
Structure Reviewer はすべての案件で起動する。

7. Tester / Security / UX Reviewer / Structure Reviewer -> Manager
テスト結果・レビュー結果を把握。作成したものに問題があれば 3 に戻る。

8. Manager -> User
最終結果、残課題、既知の制約を報告する。

## レビューループの収束条件

- Security 指摘・UX Reviewer 指摘と修正のラウンド(手順 3 および手順 7 から 3 への差し戻し)は **同一案件あたり最大 3 回まで**
- 上限は Security と UX Reviewer それぞれについて独立にカウントする
- 3 回で合意に至らない場合、`Manager` は User にエスカレーションし、判断を仰ぐ
- カウントは「設計レビュー」「実装後レビュー」を区別せず、同一案件で発生した該当レビュアー由来の差し戻し回数で数える
- Tester 由来の差し戻しはこの上限の対象外(再現性のあるテスト失敗があれば修正する)

## 実行方針

- このプロジェクトでは、原則として複数エージェントで作業する
- `Manager` は要件整理、進行管理、最終判断のみを担当する
- `Developer` は実装のみを担当する
- `Tester` は実装後の検証のみを担当する
- `Security` は必要時の設計レビューと実装レビューを担当する
- `UX Reviewer` は必要時の設計レビューと実装レビューを担当する
- `Structure Reviewer` はすべての実装後にフォルダ構成・ファイル配置のレビューを担当する

## エージェント起動ルール

- ユーザーから実装依頼を受けたら、`Manager` は先に要件を整理する
- 要件確定後、`Manager` は必ず `Developer` エージェントを起動して実装を依頼する
- 実装完了後、`Manager` は必ず `Tester` エージェントを起動して確認を依頼する
- 外部入力、認証、公開機能、外部通信、秘密情報、依存パッケージの追加・更新を扱う変更では、`Manager` は `Security` にも確認を依頼する
- ユーザーが触る画面・操作(GUI、CLI のメッセージ、ユーザー向け通知など)を含む変更では、`Manager` は `UX Reviewer` にも確認を依頼する
- 実装完了後は必ず `Structure Reviewer` にフォルダ構成の確認を依頼する
- 例外的に単一エージェントで進める場合は、その理由をユーザーに明示する

## 禁止事項

- `Manager` は実装コードを変更しない
- `Developer` は仕様書・設計書・ワークフロー文書を変更しない
- `Developer` は `work/` の外のファイルを変更しない
- 要件確定後に `Developer` を起動せず、自ら実装を開始しない
- `Tester` の確認なしに完了扱いにしない
- 例外条件が明記されていない限り、単一エージェントで全工程を兼務しない

## 初回運用ルール

- 最初は `Manager 1 / Developer 1 / Security 1 / Tester 1` で始める
- UI を持つ案件では `UX Reviewer 1` を追加する
- `Developer` の追加エージェントは、担当ファイルを明確に分けられるときだけ使う
- 同じファイルを複数の実装エージェントが触らない
- 仕様不明、テスト不能、重大なセキュリティ懸念、重大な UX 懸念が出たら `Manager` に戻す

## 実装開始条件

次が揃ってから実装を始める。

- 目的
- スコープ
- 非対象
- 受け入れ条件
- 制約

## 完了条件

次が揃って完了とする。

- 実装完了
- 変更内容の要約あり
- `Tester` によるテスト結果または未実施理由あり
- セキュリティ指摘があれば解消または受容判断あり
- UX Reviewer 指摘があれば解消または受容判断あり(UI を持つ案件のみ)
- Structure Reviewer 指摘があれば解消または受容判断あり
