# Codex Multi-Agent Workflow

## 目的

このドキュメントは、複数エージェントで開発するときの全体原則、権限境界、工程ルーターを定義する。

詳細な作業手順は `.claude/skills/` 配下の工程別 skill を参照する。
役割ごとの責務は `.claude/roles/` 配下を参照する。

## 構成パターン

案件に応じて2つの構成を使い分ける。

**通常構成（Claude Code のみ）**:
```
User → Manager (Claude Code) → Developer / Security / Tester / ...
```

**Supervisor あり構成（Codex を使う場合）**:
```
User → Supervisor (Claude Code) → Manager (Codex) → Developer / Security / Tester / ...
```

Supervisor を導入する場合、Manager 以下は構成の変更不要。
Codex の起動は Supervisor が担い、Manager から Codex をさらに起動しない（スタック防止）。
詳細は `.claude/roles/SUPERVISOR.md` を参照する。

## 工程ルーター

依頼内容に応じて、Manager（または Supervisor）は次の工程 skill を使い分ける。

| 工程 | Skill | 使う場面 | 次の工程 |
| --- | --- | --- | --- |
| 調査 | `.claude/skills/work-investigate/` | 事実、影響範囲、関係箇所、不明点を把握する | 相談 / 設計 / 終了 |
| 相談 | `.claude/skills/work-consult/` | ユーザー判断が必要な仕様、方針、優先度を決める | 調査 / 設計 / 実装 / 終了 |
| 設計 | `.claude/skills/work-design/` | 実装前に目的、スコープ、非対象、受け入れ条件、制約を確定する | 相談 / 実装 |
| 実装 | `.claude/skills/work-implement/` | Developer への実装依頼から検証、レビュー、最終報告まで進める | 設計 / 相談 / 終了 |

相談から実装へ直接進めるのは、目的、スコープ、非対象、受け入れ条件、制約がすでに揃っている場合だけとする。

## 役割

役割の一次情報は `.claude/roles/` 配下の次のファイルに置く。

- `.claude/roles/SUPERVISOR.md`（Codex 構成時のみ）
- `.claude/roles/MANAGER.md`
- `.claude/roles/DEVELOPER.md`
- `.claude/roles/SECURITY.md`
- `.claude/roles/TESTER.md`
- `.claude/roles/UX_REVIEWER.md`
- `.claude/roles/STRUCTURE_REVIEWER.md`
- `.claude/roles/SPEC_REVIEWER.md`

`.claude/skills/` は工程の進め方を定義し、`.claude/roles/` は各担当の責務と権限を定義する。
同じ責務や権限を skill 側に重複して定義しない。

## 基本方針

- ユーザーとの窓口と最終判断は `Manager` が担当する。
- 仕様が固まるまで `Developer` は実装しない。
- 要件確定後、`Manager` は自分で実装せず `Developer` を起動する。
- 実装コードを変更できるのは `Developer` のみとする。
- 仕様書、設計書、ワークフロー文書を更新できるのは `Manager` のみとする。
- コードに付随するドキュメントは `Developer` が更新できる。
- テストコード、フィクスチャ、スナップショットは `Developer` と `Tester` が更新できる。
- `Security`、`UX Reviewer`、`Structure Reviewer`、`Spec Reviewer` は原則 read-only で確認と報告を行う。
- 完了前に `Tester` の確認を必須とする。

## 成果物の配置ルール

- `Developer` が作成、変更する実装コードはすべて `work/` 配下に置く。
- `Developer` は `work/` の外を変更しない。
- `work/` 内のフォルダ構成は案件に応じて `Developer` が整える。
- ワークフロー文書と役割定義は `work/` の外に置き、`Manager` のみが更新する。

## 権限マトリクス

| Role | Spec Docs | Code Docs | Prod Code | Test Code | Test Execution | Spawn Sub-agents |
| --- | --- | --- | --- | --- | --- | --- |
| Manager | Read / Write | Read Only | Read Only | Read Only | Optional | Yes |
| Developer | Read Only | Read / Write | Read / Write | Read / Write | Yes | Yes |
| Security | Read Only | Read Only | Read Only | Read Only | Optional | No |
| Tester | Read Only | Read Only | Read Only | Read / Write | Yes | No |
| UX Reviewer | Read Only | Read Only | Read Only | Read Only | Optional | No |
| Structure Reviewer | Read Only | Read Only | Read Only | Read Only | No | No |
| Spec Reviewer | Read Only | Read Only | Read Only | Read Only | No | No |

定義:

- **Spec Docs**: 仕様書、設計書、ワークフロー文書、本ファイル、`.claude/roles/` 配下、`.claude/skills/` 配下
- **Code Docs**: コード内コメント、docstring、コードに付随する README など
- **Prod Code**: 製品として動作するアプリケーション、ライブラリのコード
- **Test Code**: 自動テスト、フィクスチャ、スナップショット、テスト用ユーティリティ

## エージェント起動ルール

- Manager は必要な工程に応じて `.claude/skills/` を選ぶ。
- 関係エージェントへ渡す文脈は、原則として `目的 / 対象 / 制約 / 依頼内容 / 期待出力` に絞る。
- 実装依頼では、Manager は必ず `Developer` を起動する。
- 実装完了後は、Manager は必ず `Tester` と `Structure Reviewer` を起動する。
- 外部入力、認証、公開機能、外部通信、秘密情報、依存パッケージの追加・更新を扱う変更では `Security` を起動する。
- ユーザーが触る画面、操作、CLI 文言、通知を含む変更では `UX Reviewer` を起動する。
- 今回の実装対象に対応する仕様書、設計書が `work/` 配下に存在する場合、または実装がそれらで定義された振る舞いに触れる場合は `Spec Reviewer` を起動する。
- 例外的に単一エージェントで進める場合は、その理由をユーザーに明示する。

## 初回運用ルール

- 最初は `Manager 1 / Developer 1 / Security 1 / Tester 1` で始める。
- UI を持つ案件では `UX Reviewer 1` を追加する。
- Developer の追加エージェントは、担当ファイルやモジュールを明確に分けられるときだけ使う。
- 同じファイルを複数の Developer が触らない。

## 停止して Manager 判断へ戻す条件

- 仕様不明。
- テスト不能。
- 設計矛盾がある。
- 重大なセキュリティ懸念がある。
- 重大な UX 懸念がある。
- スコープや受け入れ条件の根本変更が必要。

## 禁止事項

- `Manager` は実装コードを直接変更しない。
- `Developer` は仕様書、設計書、ワークフロー文書を直接更新しない。
- `Developer` は `work/` の外を変更しない。
- 要件確定後に `Developer` を起動せず、`Manager` が実装を兼務しない。
- `Tester` の確認なしに完了扱いにしない。
- 評価系ロールと実装ロールを同一セッションで兼務させない。
- 同じ権限ルールを複数の skill に重複定義しない。

## レビューループの収束条件

- Security 指摘と UX Reviewer 指摘の差し戻しは、同一案件あたりそれぞれ最大 3 回までとする。
- 3 回で合意に至らない場合、Manager は User にエスカレーションする。
- Tester 由来の差し戻しはこの上限の対象外とする。

## 実装開始条件

プログラムを書く前に、Manager は実装判断に必要な認識が User と Developer の間で揃っていることを確認する。
目的、スコープ、非対象、受け入れ条件、制約に曖昧さが残る場合は、実装を開始せず、相談または設計工程に戻す。
ここでいう曖昧さは、目的、スコープ、非対象、受け入れ条件、制約に関わるものを指す。
Developer は曖昧な点を推測で補って実装してはならない。

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
- UX Reviewer 指摘があれば解消または受容判断あり（ユーザー接点を持つ案件のみ）
- Structure Reviewer 指摘があれば解消または受容判断あり
- Spec Reviewer 指摘があれば解消または受容判断あり（今回の実装対象に対応する仕様書、設計書がある場合のみ）
