# Claude Code Workflow Manager

Claude Codeと連携し、プロジェクトの**要件定義から実装タスク分解**までを対話的に進める統合ワークフロー管理システムです。

## 概要

ソフトウェア開発において、要件の認識齟齬や設計の手戻りは大きなコストを生みます。
このワークフローシステムは、Claude Codeのカスタムスキル機能を活用し、**4つのフェーズ**で段階的にプロジェクトドキュメントを作成します。

```
要件定義 → 技術調査 → 詳細設計 → タスク分解
```

各フェーズでAIが質問を投げかけ、ユーザーが回答・承認することで、認識のズレを最小化しながら高品質な設計ドキュメントを生成します。

## 特徴

- **対話的なドキュメント作成**: 各フェーズでAIが質問→ユーザーが回答→ドキュメント生成→承認のサイクルを回す
- **依存関係の自動管理**: 前フェーズが承認されるまで次フェーズに進めない安全設計
- **状態の永続化**: 中断しても `workflow-state.json` から再開可能
- **プロジェクトタイプ別最適化**: Webアプリ、モバイルアプリ、スクレイピングシステム、APIサービス等に対応
- **ロールバック対応**: 承認後でも前フェーズに戻って修正可能

## 対応プロジェクトタイプ

| タイプ | 説明 | 特徴 |
|--------|------|------|
| `web_application` | Webアプリケーション | UI/UXフロー、デプロイタスクを含む |
| `mobile_app` | モバイルアプリ | 画面設計、ナビゲーションフローを重視 |
| `scraping_system` | スクレイピングシステム | 対象サイト分析、パーサー実装にフォーカス |
| `api_service` | APIサービス | エンドポイント設計、認証フローを詳細化 |

## クイックスタート

### 前提条件

- [Claude Code](https://claude.com/claude-code) がインストールされていること
- カスタムスキル機能が有効であること

### セットアップ

1. このリポジトリをクローン
```bash
git clone https://github.com/yutaro-create/workflow.git
```

2. `workflow.md` をClaude Codeのスキルディレクトリに配置

3. プロジェクトを開始
```bash
/workflow "作りたいプロダクトの概要"
```

### 使用例

```bash
# ECサイトを作成
/workflow "中古品を販売できるECサイト"

# タスク管理ツールを作成
/workflow "社内用のタスク管理ツール"

# 中断したプロジェクトを再開
/workflow --resume

# 現在の進捗を確認
/workflow --status
```

## ファイル構成

```
.
├── README.md                      # 本ファイル
├── workflow.md                    # メインエントリーポイント（スキル定義）
├── workflow-spec.md               # ワークフロー詳細仕様書
├── workflow-dependencies.yaml     # フェーズ間の依存関係定義
└── workflow-state-template.json   # 状態管理テンプレート
```

### 各ファイルの役割

| ファイル | 役割 |
|----------|------|
| `workflow.md` | `/workflow` コマンドのエントリーポイント。クイックスタートとコマンド一覧 |
| `workflow-spec.md` | 4フェーズの詳細プロセス、承認フロー、Q&A管理の仕様 |
| `workflow-dependencies.yaml` | フェーズの実行順序、前提条件、並行実行ルール、エラーハンドリングを定義 |
| `workflow-state-template.json` | プロジェクト状態を保存するJSONのスキーマ定義 |

## ワークフロー概要

### Phase 1: 要件定義 (`/project-requirements`)

プロジェクトの目的、ターゲットユーザー、主要機能、制約条件を明確化します。

**生成ドキュメント**: `requirements.md`

### Phase 2: 技術調査 (`/project-analysis`)

要件に基づき、プログラミング言語、フレームワーク、データベース、インフラを選定します。

**生成ドキュメント**: `tech-analysis.md`, `tech-stack.md`

### Phase 3: 詳細設計 (`/project-design`)

アーキテクチャパターン、データモデル、API設計、認証方式を決定します。

**生成ドキュメント**: `design.md`, `architecture.md`, `api-spec.md`

### Phase 4: タスク分解 (`/project-tasks`)

実装タスクを詳細分解し、優先順位と依存関係を設定します。

**生成ドキュメント**: `tasks.md`, `task-dependencies.md`

## コマンド一覧

### 基本コマンド

| コマンド | 説明 |
|----------|------|
| `/workflow "[概要]"` | 新規プロジェクト開始 |
| `/workflow --resume` | 中断したプロジェクトを再開 |
| `/workflow --status` | 現在の進捗を表示 |
| `/workflow --rollback [phase]` | 指定フェーズに戻る |

### 管理コマンド

| コマンド | 説明 |
|----------|------|
| `/workflow --reset` | プロジェクトをリセット |
| `/workflow --show-state` | 状態ファイルを表示 |
| `/workflow --show-dependencies` | 依存関係マップを表示 |
| `/workflow --debug` | デバッグモードで実行 |

## 依存関係

```
requirements (要件定義)
    ↓
analysis (技術調査)
    ↓
design (詳細設計)
    ↓
tasks (タスク分解) → 実装開始
```

各フェーズは前フェーズの**承認**が必須です。未承認のフェーズをスキップすることはできません。

## 状態管理

プロジェクトの状態は `.claude/spec/workflow-state.json` に自動保存されます。

- 質問への回答時
- フェーズ完了時
- 承認/却下時
- エラー発生時

中断しても `/workflow --resume` で続きから再開できます。

## カスタマイズ

`workflow-dependencies.yaml` を編集することで、以下のカスタマイズが可能です：

- フェーズの追加/削除
- 前提条件の変更
- プロジェクトタイプ別の設定
- バリデーションルールの調整

## 関連記事

- [Zenn記事: Claude Codeで対話的なプロジェクト設計を自動化する](#) *(公開予定)*

## ライセンス

MIT License

## Author

[@yutaro-create](https://github.com/yutaro-create)
