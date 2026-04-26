# n8n-dev-workflow
n8nを使った個人ソフトウェア開発のワークフロー環境構築

## 前提条件

このプロジェクトを使用する前に、以下のツールがインストールされている必要があります。

### 必須ツール

- **Git**: リポジトリのクローンやGitHub Actionsの使用に必要
  - インストール方法: [Git公式サイト](https://git-scm.com/downloads)
  - バージョン確認: `git --version`

- **Docker**: n8nのコンテナ実行に必要
  - インストール方法: [Docker公式サイト](https://www.docker.com/get-started)
  - バージョン確認: `docker --version`

### 必要なAPIキー・トークン

以下のAPIキーやトークンも事前に準備しておくことを推奨します（詳細は各セクションを参照）：

- **OpenAI APIキー**: CodeRabbitやCodex Issue Assistantで使用
- **GitHub Personal Access Token**: GitHub Actionsやn8nワークフローで使用
- **Anthropic APIキー**: n8nワークフローでClaudeモデルを使用する場合

## GitHub Actions ワークフローの追加方法

`github-workflow`配下にあるymlファイルをGitHub Actionsに追加するには、以下の手順を実行してください。

### 手順

1. リポジトリのルートディレクトリに`.github/workflows/`ディレクトリを作成します（存在しない場合）。

```bash
mkdir -p .github/workflows
```

2. `github-workflow`配下のymlファイルを`.github/workflows/`ディレクトリにコピーします。

```bash
cp github-workflow/*.yml .github/workflows/
```

3. 必要に応じて、各ワークフローファイルの設定を確認・調整してください。

### ワークフローの説明

- **bot-review.yml**: CodeRabbitレビューをリクエストするワークフロー（PRが開かれたときに自動実行）
- **code-rabbit.yml**: CodeRabbitによるコードレビューを実行するワークフロー（PRが開かれたときに自動実行）
- **codex-issue-assistant.yml**: Codex Issue Assistantによる自動化ワークフロー（Issueに`@codex`が含まれる場合に実行）

### CodeRabbitのOpenAIモデル設定

`code-rabbit.yml`では、`openai_light_model`と`openai_heavy_model`の2つのモデルを設定できます。

#### モデルの役割

- **openai_light_model**: 軽量なレビューや簡単な変更の分析に使用されるモデル
  - 高速でコストが低い
  - 小さな変更や単純なコードレビューに適している
  
- **openai_heavy_model**: 複雑な変更や詳細なレビューに使用されるモデル
  - 高精度だが処理時間が長く、コストが高い
  - 大規模な変更や複雑なロジックのレビューに適している

#### 使用可能なモデル

以下のOpenAIモデルが使用可能です：

| モデル名 | 説明 | 推奨用途 |
|---------|------|---------|
| `gpt-4o` | 最新のGPT-4最適化モデル（高速・高精度） | light/heavy両方に推奨 |
| `gpt-4-turbo` | GPT-4の高速版 | light/heavy両方に推奨 |
| `gpt-4` | 標準のGPT-4モデル | heavy_modelに推奨 |
| `gpt-3.5-turbo` | 軽量で高速なモデル | light_modelに推奨（コスト削減） |

#### 設定例

```yaml
# コスト重視の設定（light_modelを軽量に）
openai_light_model: gpt-3.5-turbo
openai_heavy_model: gpt-4

# バランス型の設定（両方とも最新モデル）
openai_light_model: gpt-4o
openai_heavy_model: gpt-4o

# 高精度重視の設定（両方ともGPT-4）
openai_light_model: gpt-4
openai_heavy_model: gpt-4
```

#### モデルの選択ガイド

- **コストを抑えたい場合**: `gpt-3.5-turbo`（light）と`gpt-4`（heavy）の組み合わせ
- **バランス重視**: `gpt-4o`を両方に設定
- **最高精度が必要**: `gpt-4`を両方に設定

**注意**: モデルによってAPIコストが異なります。使用量に応じて適切なモデルを選択してください。

### 必要なシークレット

一部のワークフローでは、以下のGitHub Secretsの設定が必要です：

- `OPENAI_API_KEY`: OpenAI APIキー（code-rabbit.yml、codex-issue-assistant.ymlで使用）
- `PERSONAL_ACCESS_TOKEN`: パーソナルアクセストークン（codex-issue-assistant.ymlで使用）

#### シークレットの設定手順

1. GitHubリポジトリのページにアクセスします
2. **Settings** > **Secrets and variables** > **Actions** を開きます
3. **New repository secret** ボタンをクリックします
4. 以下のシークレットをそれぞれ追加します：

##### OPENAI_API_KEY の設定

1. **Name**: `OPENAI_API_KEY` と入力
2. **Secret**: OpenAI APIキーを入力
   - OpenAI APIキーは [OpenAI Platform](https://platform.openai.com/api-keys) から取得できます
   - 新しいAPIキーを作成する場合は、**Create new secret key** をクリック
3. **Add secret** をクリックして保存

##### PERSONAL_ACCESS_TOKEN の設定

1. **Name**: `PERSONAL_ACCESS_TOKEN` と入力
2. **Secret**: GitHub Personal Access Tokenを入力
   - Personal Access Tokenは以下の手順で作成します：
     - GitHubの **Settings** > **Developer settings** > **Personal access tokens** > **Tokens (classic)** にアクセス
     - **Generate new token** > **Generate new token (classic)** をクリック
     - トークン名を入力（例: `n8n-dev-workflow`）
     - 必要なスコープを選択：
       - `repo` (リポジトリへのフルアクセス)
       - `workflow` (GitHub Actionsワークフローの更新)
     - **Generate token** をクリック
     - 表示されたトークンをコピー（この画面を閉じると二度と表示されません）
3. **Add secret** をクリックして保存

#### 注意事項

- シークレットは一度設定すると、値の確認はできません（再設定が必要です）
- シークレットはワークフロー実行時に環境変数として利用可能になります
- Personal Access Tokenは定期的に更新することを推奨します

## n8n のセットアップ方法

`n8n`フォルダにあるDocker Composeファイルを使用してn8n環境を構築します。

### 前提条件

- Docker と Docker Compose がインストールされていること

### セットアップ手順

1. **n8nフォルダに移動します**

```bash
cd n8n
```

2. **環境変数ファイル（.env）を作成します**

```bash
cat > .env << EOF
N8N_ENCRYPTION_KEY=$(openssl rand -hex 32)
TZ=Asia/Tokyo
EOF
```

または、手動で`.env`ファイルを作成して以下の内容を設定します：

```bash
N8N_ENCRYPTION_KEY=ここに32バイトのランダムキーを設定
TZ=Asia/Tokyo
```

**重要**: `N8N_ENCRYPTION_KEY`はn8nの認証情報などを暗号化するために不可欠です。安全な場所に保管してください。

3. **Docker Composeでn8nを起動します**

```bash
docker compose up -d
```

4. **n8nにアクセスします**

Webブラウザで `http://localhost:5678` を開きます。

### 設定ファイルの説明

- **docker-compose.yml**: n8nサービスの設定ファイル
  - ポート: `5678`で公開
  - データはDockerボリューム `n8n-data` に永続化されます
  - バージョンを固定する場合は、`image: n8nio/n8n:latest` を `image: n8nio/n8n:1.28.0` のように変更できます

- **.env**: 環境変数ファイル（Git管理対象外）
  - `N8N_ENCRYPTION_KEY`: n8nの暗号化キー（32バイト推奨）
  - `TZ`: タイムゾーン設定

### よく使うコマンド

- **n8nの停止**: `docker compose down`
- **n8nの再起動**: `docker compose restart`
- **ログの確認**: `docker compose logs -f`
- **データのバックアップ**: Dockerボリューム `n8n-data` をエクスポート

### トラブルシューティング

- ポート5678が既に使用されている場合は、`docker-compose.yml`の`ports`セクションを変更してください
- データが消えた場合は、Dockerボリューム `n8n-data` を確認してください

## n8nワークフローのインポート方法

`n8n-workflow`フォルダにあるワークフローファイル（JSON形式）をn8nにインポートする方法です。

### インポート手順

1. **n8nにアクセスします**

   Webブラウザで `http://localhost:5678` を開きます。

2. **ワークフローをインポートします**

   - n8nのトップページで、右上の **「+」** ボタンまたは **「Add workflow」** をクリック
   - メニューから **「Import from File」** を選択
   - `n8n-workflow`フォルダにあるJSONファイル（例: `dev-workflow.json`）を選択してアップロード

   または、以下の手順でもインポートできます：

   - ワークフロー一覧ページで、右上の **「⋮」**（三点メニュー）をクリック
   - **「Import from File」** を選択
   - JSONファイルを選択してアップロード

3. **認証情報を設定します**

   インポートしたワークフローには、外部サービス（GitHub、Anthropicなど）への接続に必要な認証情報が含まれていない場合があります。以下の手順で認証情報を設定してください：

   - ワークフローを開く
   - 認証情報が必要なノード（赤い警告マークが表示される）をクリック
   - **「Credential」** セクションで、該当する認証情報を選択または作成
   - 例：
     - **GitHub**: GitHubアカウントのPersonal Access Tokenを設定
     - **Anthropic**: Anthropic APIキーを設定

4. **ワークフローを有効化します**

   - ワークフローの右上にある **「Inactive」** トグルを **「Active」** に切り替えると、ワークフローが実行可能になります

### ワークフローファイルの説明

- **My workflow.json**: サンプルワークフローファイル
  - チャットメッセージを受信
  - AnthropicのClaudeモデルでメッセージを処理
  - GitHubにIssueを作成

### 注意事項

- インポートしたワークフローには、元のワークフローで使用されていた認証情報のIDが含まれている場合がありますが、実際の認証情報は別途設定する必要があります
- ワークフロー内のリポジトリ名やオーナー名などは、環境に合わせて変更してください
- ワークフローを有効化する前に、すべての認証情報が正しく設定されていることを確認してください