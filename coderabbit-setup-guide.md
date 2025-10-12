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

## 参考リンク

- [CodeRabbit公式サイト](https://coderabbit.ai)
- [CodeRabbit Documentation](https://docs.coderabbit.ai)
- [GitHub Actions for CodeRabbit](https://github.com/coderabbitai/coderabbit)
- [CodeRabbit CLI Installation](https://cli.coderabbit.ai)
- [OpenAI API Keys](https://platform.openai.com/api-keys)
