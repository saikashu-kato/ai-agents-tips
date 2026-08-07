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

## 2. VS Code プラグインを使用する方法

### 環境要件
- Visual Studio Code
- CodeRabbitアカウント
- Gitリポジトリ

### インストール手順

#### ステップ1: プラグインのインストール
1. VS Codeを開く
2. 拡張機能タブ（`Ctrl+Shift+X`）を開く
3. 「CodeRabbit」で検索
4. 「CodeRabbit」プラグインをインストール
5. VS Codeを再起動

#### ステップ2: 認証設定
1. VS Codeでコマンドパレット（`Ctrl+Shift+P`）を開く
2. 「CodeRabbit: Login」を実行
3. ブラウザでCodeRabbitアカウントにログイン
4. 認証完了後、VS Codeに戻る

#### ステップ3: リポジトリの設定
1. Gitリポジトリを開く
2. コマンドパレットで「CodeRabbit: Setup Repository」を実行
3. リポジトリの設定を確認・完了

### 基本的な使用方法

#### コードレビューの実行
```bash
# コマンドパレットから実行
Ctrl+Shift+P → "CodeRabbit: Review Current File"
Ctrl+Shift+P → "CodeRabbit: Review Selection"
Ctrl+Shift+P → "CodeRabbit: Review Repository"
```

#### キーボードショートカット
- `Ctrl+Shift+R`: 現在のファイルをレビュー
- `Ctrl+Shift+S`: 選択したコードをレビュー
- `Ctrl+Shift+T`: リポジトリ全体をレビュー

### 設定とカスタマイズ

#### 設定ファイル（settings.json）
```json
{
  "coderabbit.enabled": true,
  "coderabbit.autoReview": true,
  "coderabbit.language": "ja",
  "coderabbit.profile": "chill",
  "coderabbit.maxComments": 10,
  "coderabbit.excludePatterns": [
    "node_modules/**",
    "dist/**",
    "build/**"
  ],
  "coderabbit.includePatterns": [
    "src/**",
    "lib/**"
  ]
}
```

#### ワークスペース設定
```json
{
  "coderabbit.workspace.enabled": true,
  "coderabbit.workspace.configFile": ".coderabbit.yml",
  "coderabbit.workspace.autoReview": true,
  "coderabbit.workspace.notifications": true
}
```

### 高度な設定

#### カスタムレビュー設定
```json
{
  "coderabbit.customInstructions": {
    "focusAreas": [
      "security",
      "performance",
      "maintainability"
    ],
    "tone": "professional",
    "language": "ja"
  }
}
```

#### ファイル別設定
```json
{
  "coderabbit.fileSettings": {
    "**/*.js": {
      "focusAreas": ["performance", "security"],
      "customInstructions": "JavaScriptのベストプラクティスに従ってレビューしてください"
    },
    "**/*.py": {
      "focusAreas": ["maintainability", "performance"],
      "customInstructions": "PEP8規約に従ってレビューしてください"
    }
  }
}
```

### 統合機能

#### Git統合
- **プルリクエスト**: 自動レビュー
- **ブランチ**: ブランチ別レビュー設定
- **コミット**: コミットメッセージの提案

#### エディタ統合
- **インラインコメント**: コード内での直接コメント
- **問題パネル**: レビュー結果の表示
- **クイックフィックス**: 自動修正提案

### トラブルシューティング

#### 認証エラー
```bash
# 認証情報のクリア
Ctrl+Shift+P → "CodeRabbit: Logout"
Ctrl+Shift+P → "CodeRabbit: Login"
```

#### 接続エラー
```bash
# 接続状態の確認
Ctrl+Shift+P → "CodeRabbit: Check Connection"
```

#### 設定のリセット
```bash
# 設定のリセット
Ctrl+Shift+P → "CodeRabbit: Reset Settings"
```

### 推奨設定

#### 個人開発者向け
```json
{
  "coderabbit.enabled": true,
  "coderabbit.autoReview": true,
  "coderabbit.language": "ja",
  "coderabbit.profile": "chill",
  "coderabbit.notifications": true
}
```

#### チーム開発向け
```json
{
  "coderabbit.enabled": true,
  "coderabbit.autoReview": false,
  "coderabbit.language": "ja",
  "coderabbit.profile": "assertive",
  "coderabbit.teamMode": true,
  "coderabbit.notifications": true
}
```

---

## 3. CodeRabbit CLI を使用する方法

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

## 4. ダッシュボードを使用する方法

### 環境要件
- CodeRabbitアカウント
- Gitサービスアカウント（GitHub、GitLab、Bitbucket、Azure DevOps等）

### セットアップ手順

#### ステップ1: アカウント作成
1. [CodeRabbit公式サイト](https://coderabbit.ai) にアクセス
2. 「Sign Up」をクリック
3. 対応するGitサービスアカウントでログイン

#### ステップ2: リポジトリの連携
1. ダッシュボードで「Add Repository」をクリック
2. 連携したいリポジトリを選択
3. 権限設定を確認・承認

#### ステップ3: 設定のカスタマイズ
1. リポジトリ設定でレビュー設定を調整
2. 通知設定を構成
3. チームメンバーの招待

### 対応Gitサービス別セットアップ

#### GitHub
**前提条件:**
- GitHubアカウント
- リポジトリへのアクセス権限

**セットアップ手順:**
1. CodeRabbitダッシュボードで「GitHub」を選択
2. GitHubアカウントでOAuth認証
3. 連携したいリポジトリを選択
4. 権限設定（Read/Write）を確認・承認

#### GitLab
**前提条件:**
- GitLabアカウント（GitLab.com または セルフホスト）
- リポジトリへのアクセス権限

**セットアップ手順:**
1. CodeRabbitダッシュボードで「GitLab」を選択
2. GitLabアカウントでOAuth認証
3. 連携したいリポジトリを選択
4. 権限設定を確認・承認

**セルフホストGitLabの場合:**
```bash
# GitLabインスタンスのURL設定
# 例: https://gitlab.yourcompany.com
```

#### Bitbucket
**前提条件:**
- Bitbucketアカウント
- リポジトリへのアクセス権限

**セットアップ手順:**
1. CodeRabbitダッシュボードで「Bitbucket」を選択
2. BitbucketアカウントでOAuth認証
3. 連携したいリポジトリを選択
4. 権限設定を確認・承認

#### Azure DevOps
**前提条件:**
- Azure DevOpsアカウント
- プロジェクトへのアクセス権限

**セットアップ手順:**
1. CodeRabbitダッシュボードで「Azure DevOps」を選択
2. MicrosoftアカウントでOAuth認証
3. 連携したいプロジェクト・リポジトリを選択
4. 権限設定を確認・承認

**Azure DevOps Server（オンプレミス）の場合:**
```bash
# カスタムURL設定
# 例: https://devops.yourcompany.com
```

### 複数Gitサービスの連携

#### マルチプロバイダー設定
```yaml
# .coderabbit.yml での設定例
reviews:
  auto_review:
    enabled: true
    # 複数のGitサービスに対応
    providers:
      - github
      - gitlab
      - bitbucket
      - azure_devops
```

#### 権限管理
- **GitHub**: Personal Access Token または OAuth
- **GitLab**: Personal Access Token または OAuth
- **Bitbucket**: App Password または OAuth
- **Azure DevOps**: Personal Access Token または OAuth

### セキュリティ設定

#### アクセストークンの管理
```bash
# 環境変数での設定例
export GITHUB_TOKEN="your-github-token"
export GITLAB_TOKEN="your-gitlab-token"
export BITBUCKET_TOKEN="your-bitbucket-token"
export AZURE_DEVOPS_TOKEN="your-azure-devops-token"
```

#### 権限の最小化
- **Read権限**: コードレビューのみ
- **Write権限**: コメント投稿、ラベル設定
- **Admin権限**: 設定変更、チーム管理

### トラブルシューティング

#### 認証エラー
```bash
# トークンの有効性確認
curl -H "Authorization: token YOUR_TOKEN" https://api.github.com/user
curl -H "Authorization: Bearer YOUR_TOKEN" https://gitlab.com/api/v4/user
```

#### 権限エラー
- リポジトリのアクセス権限を確認
- 組織の設定でCodeRabbitを許可
- プライベートリポジトリの場合は適切な権限を設定

#### 連携の確認
```bash
# 各サービスのAPI接続確認
# GitHub
curl -H "Authorization: token YOUR_TOKEN" https://api.github.com/repos/owner/repo

# GitLab
curl -H "Authorization: Bearer YOUR_TOKEN" https://gitlab.com/api/v4/projects/PROJECT_ID

# Bitbucket
curl -u "username:password" https://api.bitbucket.org/2.0/repositories/owner/repo

# Azure DevOps
curl -u ":YOUR_TOKEN" https://dev.azure.com/organization/project/_apis/git/repositories
```

### プラン比較

| 機能 | 無料プラン | 有料プラン |
|------|------------|------------|
| **プルリクエストサマリー** | ✓ | ✓ |
| **詳細レビュー** | - | ✓ |
| **セキュリティチェック** | - | ✓ |
| **カスタムルール** | - | ✓ |
| **チーム機能** | 制限あり | ✓ |

---

## 5. 比較表

### 導入方法の比較

| 項目 | GitHub Actions | VS Code | CLI | ダッシュボード |
|------|----------------|---------|-----|----------------|
| **セットアップ** | 中 | 低 | 高 | 低 |
| **カスタマイズ** | 高 | 中 | 最高 | 中 |
| **チーム共有** | 高 | 中 | 低 | 最高 |
| **コスト** | API使用料 | 無料 | 無料 | プラン料金 |
| **自動化** | 最高 | 中 | 中 | 最高 |
| **学習コスト** | 中 | 低 | 高 | 低 |
| **リアルタイム** | ✗ | ✓ | ✗ | ✗ |
| **IDE統合** | ✗ | ✓ | ✗ | ✗ |

### Gitサービス別対応状況

| Gitサービス | GitHub Actions | VS Code | CLI | ダッシュボード | 備考 |
|-------------|----------------|---------|-----|----------------|------|
| **GitHub** | ✓ | ✓ | ✓ | ✓ | 完全対応 |
| **GitLab** | △ | ✓ | ✓ | ✓ | セルフホスト対応 |
| **Bitbucket** | △ | ✓ | ✓ | ✓ | クラウド版対応 |
| **Azure DevOps** | △ | ✓ | ✓ | ✓ | オンプレミス対応 |
| **GitHub Enterprise** | ✓ | ✓ | ✓ | ✓ | エンタープライズ対応 |
| **GitLab CE/EE** | △ | ✓ | ✓ | ✓ | コミュニティ/エンタープライズ版 |

**凡例:**
- ✓: 完全対応
- △: 制限あり（設定が必要）
- ✗: 非対応

### 機能別対応状況

| 機能 | GitHub | GitLab | Bitbucket | Azure DevOps |
|------|--------|--------|-----------|--------------|
| **プルリクエストレビュー** | ✓ | ✓ | ✓ | ✓ |
| **マージリクエストレビュー** | - | ✓ | - | - |
| **ブランチ保護** | ✓ | ✓ | ✓ | ✓ |
| **Webhook連携** | ✓ | ✓ | ✓ | ✓ |
| **API連携** | ✓ | ✓ | ✓ | ✓ |
| **SSO認証** | ✓ | ✓ | ✓ | ✓ |
| **組織管理** | ✓ | ✓ | ✓ | ✓ |

---

## 6. 推奨事項

### 個人開発者
1. **VS Codeプラグイン**から開始
   - セットアップが最も簡単
   - リアルタイムレビュー
   - IDE統合による効率的な作業

2. **GitHub Actions**を併用
   - 自動化レベルが高い
   - コスト効率が良い

### チーム開発
1. **ダッシュボード**を推奨
   - チーム管理機能が充実
   - 設定の一元管理
   - 権限管理が容易

2. **VS Codeプラグイン**を併用
   - 個人作業での効率化
   - リアルタイムフィードバック

### 高度なカスタマイズ
1. **CLI**を選択
   - 最大限のカスタマイズ
   - スクリプト化可能
   - ローカル実行

### 開発環境別推奨事項

#### VS Codeユーザー
- **個人**: VS Codeプラグイン + GitHub Actions
- **チーム**: VS Codeプラグイン + ダッシュボード
- **エンタープライズ**: VS Codeプラグイン + CLI

#### その他のIDEユーザー
- **個人**: GitHub Actions + ダッシュボード
- **チーム**: ダッシュボード中心
- **エンタープライズ**: CLI + ダッシュボード

### Gitサービス別推奨事項

#### GitHub
- **個人**: GitHub Actions + ダッシュボード
- **チーム**: ダッシュボード中心
- **エンタープライズ**: GitHub Enterprise + ダッシュボード

#### GitLab
- **個人**: CLI + ダッシュボード
- **チーム**: ダッシュボード中心
- **セルフホスト**: CLI + カスタム設定

#### Bitbucket
- **個人**: ダッシュボード
- **チーム**: ダッシュボード + CLI
- **エンタープライズ**: ダッシュボード中心

#### Azure DevOps
- **個人**: ダッシュボード
- **チーム**: ダッシュボード + CLI
- **オンプレミス**: CLI + カスタム設定

### マルチプロバイダー環境
1. **統一管理**: ダッシュボードで複数サービスを統合
2. **個別最適化**: サービスごとにCLIでカスタマイズ
3. **段階的導入**: 主要サービスから開始し、段階的に拡張

---

## 7. トラブルシューティング

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

#### VS Code プラグインのエラー
```bash
# プラグインの再インストール
# VS Code拡張機能タブで「CodeRabbit」をアンインストール
# 再インストール

# 設定のリセット
Ctrl+Shift+P → "CodeRabbit: Reset Settings"

# ログの確認
Ctrl+Shift+P → "CodeRabbit: Show Logs"
```

### サポート

- **公式ドキュメント**: [CodeRabbit Documentation](https://docs.coderabbit.ai)
- **GitHub Issues**: [CodeRabbit Issues](https://github.com/coderabbitai/coderabbit/issues)
- **コミュニティ**: [CodeRabbit Community](https://community.coderabbit.ai)

---

## 8. まとめ

CodeRabbitは、AIを活用したコードレビューの自動化により、開発効率とコード品質の向上を実現する強力なツールです。

**推奨アプローチ:**
1. VS Codeユーザーはプラグインから開始
2. 個人開発者はVS Codeプラグイン + GitHub Actions
3. チーム開発ではダッシュボードを活用
4. 高度なカスタマイズが必要な場合はCLIを使用
5. 段階的に機能を拡張していく

適切な設定と使用方法により、CodeRabbitは開発ワークフローを大幅に効率化するでしょう。

---

## 9. coderabbit.yaml 設定項目詳細

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
