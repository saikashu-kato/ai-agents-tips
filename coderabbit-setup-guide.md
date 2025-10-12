# CodeRabbit 環境構築ガイド

## 概要

CodeRabbitは、AIを活用したコードレビューを自動化するツールです。プルリクエストに対して自動でレビューコメントを追加し、開発効率とコード品質の向上を実現します。

## 前提条件

- **GitHubリポジトリ**へのアクセス権限
- **OpenAI API Key**（GitHub Actions使用時）
- **Node.js**（CLI使用時、推奨）

---

## 1. GitHub Actions を使用する方法

### 環境要件
- GitHubリポジトリの管理権限
- OpenAI API Key
- プルリクエストが有効なリポジトリ

### インストール手順

#### ステップ1: ワークフローファイルの作成
1. リポジトリのルートに `.github/workflows/` ディレクトリを作成
2. `ai-review.yaml` ファイルを作成
3. 以下の内容をコピー：

```yaml
name: AI Code Review

on:
  pull_request:
    types: [opened, synchronize, reopened]

jobs:
  review:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - name: CodeRabbit Review
        uses: coderabbitai/coderabbit@latest
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          openai_api_key: ${{ secrets.OPENAI_API_KEY }}
```

#### ステップ2: GitHubシークレットの設定
1. リポジトリの「Settings」→「Secrets and variables」→「Actions」
2. 「New repository secret」をクリック
3. 名前: `OPENAI_API_KEY`
4. 値: OpenAI API Keyを入力
5. 「Add secret」をクリック

#### ステップ3: 動作確認
1. 新しいプルリクエストを作成
2. 自動的にAIレビューが実行されることを確認
3. レビューコメントが追加されることを確認

### 設定オプション

#### カスタム設定の追加
```yaml
- name: CodeRabbit Review
  uses: coderabbitai/coderabbit@latest
  with:
    github_token: ${{ secrets.GITHUB_TOKEN }}
    openai_api_key: ${{ secrets.OPENAI_API_KEY }}
    # カスタム設定
    review_type: "full"  # full, summary, security
    language: "ja"       # 日本語でのレビュー
    max_comments: 10     # 最大コメント数
```

---

## 2. CodeRabbit CLI を使用する方法

### 環境要件
- macOS または Linux
- Node.js（推奨）
- ターミナルアクセス

### インストール手順

#### ステップ1: CLIのインストール
```bash
# 公式インストールスクリプトを実行
curl -fsSL https://cli.coderabbit.ai/install.sh | sh
```

#### ステップ2: シェル設定のリロード
```bash
# zshを使用している場合
source ~/.zshrc

# bashを使用している場合
source ~/.bashrc
```

#### ステップ3: インストール確認
```bash
coderabbit --help
```

### 基本的な使用方法

#### プロジェクトの初期化
```bash
# リポジトリでCodeRabbitを初期化
coderabbit init
```

#### コードレビューの実行
```bash
# 現在のブランチをレビュー
coderabbit review

# 特定のファイルをレビュー
coderabbit review src/main.js

# プルリクエストのレビュー
coderabbit review --pr 123
```

#### 設定ファイルの作成
```bash
# 設定ファイルを生成
coderabbit config --init
```

### 設定ファイル（.coderabbit.yml）の例
```yaml
# CodeRabbit設定
review:
  type: "full"           # full, summary, security
  language: "ja"         # レビュー言語
  max_comments: 10       # 最大コメント数
  focus_areas:           # 重点レビュー領域
    - "security"
    - "performance"
    - "maintainability"

# 除外設定
exclude:
  patterns:
    - "*.test.js"
    - "node_modules/**"
    - "dist/**"
```

---

## 3. ダッシュボードを使用する方法

### 環境要件
- CodeRabbitアカウント
- GitHubアカウントとの連携

### セットアップ手順

#### ステップ1: アカウント作成
1. [CodeRabbit公式サイト](https://coderabbit.ai) にアクセス
2. 「Sign Up」をクリック
3. GitHubアカウントでログイン

#### ステップ2: リポジトリの連携
1. ダッシュボードで「Add Repository」をクリック
2. 連携したいリポジトリを選択
3. 権限設定を確認・承認

#### ステップ3: 設定のカスタマイズ
1. リポジトリ設定でレビュー設定を調整
2. 通知設定を構成
3. チームメンバーの招待

### プラン比較

| 機能 | 無料プラン | 有料プラン |
|------|------------|------------|
| **プルリクエストサマリー** | ✓ | ✓ |
| **詳細レビュー** | - | ✓ |
| **セキュリティチェック** | - | ✓ |
| **カスタムルール** | - | ✓ |
| **チーム機能** | 制限あり | ✓ |

---

## 4. 比較表

| 項目 | GitHub Actions | CLI | ダッシュボード |
|------|----------------|-----|----------------|
| **セットアップ** | 中 | 高 | 低 |
| **カスタマイズ** | 高 | 最高 | 中 |
| **チーム共有** | 高 | 低 | 最高 |
| **コスト** | API使用料 | 無料 | プラン料金 |
| **自動化** | 最高 | 中 | 最高 |
| **学習コスト** | 中 | 高 | 低 |

---

## 5. 推奨事項

### 個人開発者
1. **GitHub Actions**から開始
   - セットアップが簡単
   - 自動化レベルが高い
   - コスト効率が良い

### チーム開発
1. **ダッシュボード**を推奨
   - チーム管理機能が充実
   - 設定の一元管理
   - 権限管理が容易

### 高度なカスタマイズ
1. **CLI**を選択
   - 最大限のカスタマイズ
   - スクリプト化可能
   - ローカル実行

---

## 6. トラブルシューティング

### よくある問題

#### GitHub Actions が動作しない
```bash
# ワークフローファイルの構文確認
# シークレットの設定確認
# リポジトリの権限確認
```

#### CLI のインストールエラー
```bash
# 権限の確認
sudo chown -R $(whoami) /usr/local/bin

# 再インストール
curl -fsSL https://cli.coderabbit.ai/install.sh | sh
```

#### API Key の設定エラー
```bash
# 環境変数の確認
echo $OPENAI_API_KEY

# 設定の再実行
export OPENAI_API_KEY="your-api-key-here"
```

### サポート

- **公式ドキュメント**: [CodeRabbit Documentation](https://docs.coderabbit.ai)
- **GitHub Issues**: [CodeRabbit Issues](https://github.com/coderabbitai/coderabbit/issues)
- **コミュニティ**: [CodeRabbit Community](https://community.coderabbit.ai)

---

## 7. まとめ

CodeRabbitは、AIを活用したコードレビューの自動化により、開発効率とコード品質の向上を実現する強力なツールです。

**推奨アプローチ:**
1. 個人開発者はGitHub Actionsから開始
2. チーム開発ではダッシュボードを活用
3. 高度なカスタマイズが必要な場合はCLIを使用
4. 段階的に機能を拡張していく

適切な設定と使用方法により、CodeRabbitは開発ワークフローを大幅に効率化するでしょう。

---

## 8. coderabbit.yaml 設定項目詳細

### 基本設定

#### language
```yaml
language: "ja"  # または "ja-JP"
```
- **説明**: レビュー言語を指定（ISOコード形式）
- **デフォルト**: `en-US`
- **日本語設定**: `ja` または `ja-JP`

#### tone_instructions
```yaml
tone_instructions: "必ずダースベーダー調の話し方にしてください。"
```
- **説明**: レビューのトーンを指定（最大250文字）
- **デフォルト**: 空文字

#### early_access
```yaml
early_access: true
```
- **説明**: 早期アクセス機能を有効化
- **デフォルト**: `false`

#### enable_free_tier
```yaml
enable_free_tier: true
```
- **説明**: 無課金ユーザーに無料プラン機能を提供
- **デフォルト**: `true`

### レビュー設定（reviews）

#### profile
```yaml
reviews:
  profile: "chill"  # または "assertive"
```
- **説明**: レビューのプロフィール設定
- **chill**: リラックスしたトーン
- **assertive**: アグレッシブなトーン
- **デフォルト**: `chill`

#### request_changes_workflow
```yaml
reviews:
  request_changes_workflow: false
```
- **説明**: CodeRabbitのコメント解決時にレビューを承認するか
- **デフォルト**: `false`

#### high_level_summary
```yaml
reviews:
  high_level_summary: true
  high_level_summary_placeholder: "@coderabbitai summary"
  high_level_summary_in_walkthrough: false
```
- **説明**: PRに変更の高レベル要約を含める
- **デフォルト**: `true`

#### auto_title
```yaml
reviews:
  auto_title_placeholder: "@coderabbitai"
  auto_title_instructions: "タイトル生成の指示"
```
- **説明**: 自動タイトル生成機能
- **デフォルト**: `@coderabbitai`

#### review_status
```yaml
reviews:
  review_status: true
  commit_status: true
  fail_commit_status: false
```
- **説明**: レビューステータスの表示設定
- **デフォルト**: `true`

#### walkthrough設定
```yaml
reviews:
  collapse_walkthrough: false
  changed_files_summary: true
  sequence_diagrams: true
  poem: true
```
- **説明**: ウォークスルー関連の設定
- **デフォルト**: 各種設定あり

#### 課題・PR関連
```yaml
reviews:
  assess_linked_issues: true
  related_issues: true
  related_prs: true
  suggested_labels: true
  auto_apply_labels: false
  suggested_reviewers: true
  auto_assign_reviewers: false
```
- **説明**: 課題・PR・ラベル・レビュアー関連の設定
- **デフォルト**: 各種設定あり

### パス設定

#### path_filters
```yaml
reviews:
  path_filters:
    - "src/**"      # 含めるパターン
    - "!docs/**"    # 除外パターン（!で始まる）
    - "!node_modules/**"
```
- **説明**: レビュー対象ファイルのフィルタリング
- **除外**: `!` で始まるパターンは除外

#### path_instructions
```yaml
reviews:
  path_instructions:
    - path: "src/**"
      instructions: |
        Reactコンポーネントに関する変更を含む場合、
        以下の点を重点的にレビューしてください：
        - パフォーマンスの最適化
        - アクセシビリティの確保
        - コンポーネントの再利用性
```
- **説明**: パス別のレビューガイドライン（最大2,000文字）

### 自動レビュー設定

#### auto_review
```yaml
reviews:
  auto_review:
    enabled: true
    drafts: false
    base_branches: ["main", "develop"]
```
- **説明**: 自動レビューの有効化
- **drafts**: ドラフトPRも対象にするか
- **base_branches**: 対象ブランチの指定

### チャット機能

#### chat
```yaml
chat:
  auto_reply: true
```
- **説明**: チャット機能の自動返信
- **デフォルト**: 設定なし

### 外部連携

#### linear
```yaml
linear:
  api_key: "your-linear-api-key"
  team_key: "your-team-key"
```
- **説明**: Linearとの連携設定
- **team_key**: Linearのチーム設定で確認

#### pull_requests
```yaml
pull_requests:
  scope: "auto"  # local, global, auto
```
- **説明**: PRのスコープ設定
- **local**: リポジトリのPRのみ
- **global**: 組織のPRのみ
- **auto**: 公開リポジトリはリポジトリ、非公開は組織

### コード生成設定

#### docstrings
```yaml
code_generation:
  docstrings:
    language: "ja"
    path_instructions:
      - path: "src/**"
        instructions: "日本語でドキュメンテーション文字列を生成してください"
```
- **説明**: ドキュメンテーション文字列の生成設定

#### unit_tests
```yaml
code_generation:
  unit_tests:
    path_instructions:
      - path: "src/**"
        instructions: "Jestを使用したユニットテストを生成してください"
```
- **説明**: ユニットテストの生成設定

### その他の設定

#### abort_on_close
```yaml
abort_on_close: true
```
- **説明**: PRクローズ・マージ時のレビュー中止
- **デフォルト**: `true`

#### disable_cache
```yaml
disable_cache: false
```
- **説明**: キャッシュの無効化
- **デフォルト**: `false`

#### remote_config
```yaml
remote_config:
  url: "https://your-config-location/.coderabbit.yaml"
```
- **説明**: 外部設定ファイルの読み込み

### 設定例

#### 基本的な設定例
```yaml
# yaml-language-server: $schema=https://coderabbit.ai/integrations/schema.v2.json
language: "ja"
tone_instructions: "日本語で丁寧なレビューをお願いします。"
reviews:
  profile: "chill"
  high_level_summary: true
  poem: true
  auto_review:
    enabled: true
    base_branches: ["main"]
  path_instructions:
    - path: "src/**"
      instructions: |
        以下の点を重点的にレビューしてください：
        - コードの可読性
        - パフォーマンス
        - セキュリティ
        - テストの網羅性
```

#### 高度な設定例
```yaml
# yaml-language-server: $schema=https://coderabbit.ai/integrations/schema.v2.json
language: "ja"
early_access: true
tone_instructions: "エンタープライズ開発チームの専門家として、簡潔で明確なコードレビューを提供してください。"
reviews:
  profile: "assertive"
  request_changes_workflow: true
  high_level_summary: true
  review_status: true
  commit_status: true
  auto_review:
    enabled: true
    drafts: false
    base_branches: ["main", "develop", "release"]
  path_filters:
    - "src/**"
    - "!node_modules/**"
    - "!dist/**"
  path_instructions:
    - path: "src/components/**"
      instructions: |
        Reactコンポーネントのレビューガイドライン：
        - パフォーマンス最適化（memo化、useCallback等）
        - アクセシビリティ（ARIA属性、キーボード操作）
        - プロップタイプの適切な定義
        - エラーハンドリングの実装
    - path: "src/utils/**"
      instructions: |
        ユーティリティ関数のレビューガイドライン：
        - 純粋関数の実装
        - エラーハンドリング
        - 型安全性
        - テストの網羅性
  labeling_instructions:
    - label: "bug"
      instructions: "バグ修正に関する変更"
    - label: "enhancement"
      instructions: "新機能追加に関する変更"
    - label: "frontend"
      instructions: "フロントエンド関連の変更"
chat:
  auto_reply: true
```

### ベストプラクティス

1. **段階的な設定**: まずはデフォルト設定で開始し、必要に応じてカスタマイズ
2. **パス別設定**: プロジェクト構造に応じてパス別のレビューガイドラインを設定
3. **チーム共有**: 組織で共通の設定ファイルを使用する場合は `remote_config` を活用
4. **定期的な見直し**: プロジェクトの成長に合わせて設定を調整

---

## 参考リンク

- [CodeRabbit公式サイト](https://coderabbit.ai)
- [CodeRabbit Documentation](https://docs.coderabbit.ai)
- [GitHub Actions for CodeRabbit](https://github.com/coderabbitai/coderabbit)
- [CodeRabbit CLI Installation](https://cli.coderabbit.ai)
- [OpenAI API Keys](https://platform.openai.com/api-keys)
- [CodeRabbit設定項目一覧（Qiita）](https://qiita.com/goofmint/items/a7908530f8ec748a7538)
