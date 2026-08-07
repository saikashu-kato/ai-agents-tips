# OpenClaw 環境構築手順

OpenClaw は、Slack・Discord・Telegram などのチャット経由で AI エージェントを動かせるオープンソースの AI アシスタントフレームワークです。本書では **Docker を使う構成**と **Docker を使わない構成**の 2 パターンをまとめます。

**前提（共通）**: OpenClaw は動作環境や接続ツールによって強い権限を持ちます。セキュリティリスクを判断できる人が、最小限の権限で利用することを推奨します。Docker でも「完全なセキュリティ境界ではない」と公式に述べられていますが、素のホスト実行より被害範囲を限定しやすいです。

**参考記事**

- [【第1回】OpenClawをDocker環境で安全に動かす──Anthropic APIキーの設定からチャット開始まで](https://zenn.dev/and_dot/articles/ef838bede2604f)（Zenn / アンドドット）
- [OpenClawを日本語環境で使い倒す ── セットアップから実践まで](https://qiita.com/nogataka/items/34cfbee988a9cd873c91)（Qiita）

---

## パターン A: Docker で構築する

公式の `docker-setup.sh` ではなく、[alpine/openclaw](https://hub.docker.com/r/alpine/openclaw) イメージと `docker-compose`・設定ファイルで動かす手順です。ホストに Node.js を入れず、設定の中身を把握しやすいのが利点です。

### 前提条件

| 要件 | 内容 |
| --- | --- |
| Docker Desktop または Docker Engine | 最新安定版 |
| Docker Compose | **v2 以上**（`docker compose` 形式） |
| メモリ | 2GB 以上（記事例ではコンテナに `memory: 2G` など） |
| ディスク | 5GB 以上の空き |
| Anthropic API キー | `sk-ant-` で始まるもの（[Anthropic Console](https://console.anthropic.com/) で発行） |

### 手順

#### 1. ハンズオンリポジトリを取得する

```bash
git clone https://github.com/p0x0q/openclaw-hands-on.git
cd openclaw-hands-on/step1-docker-setup
```

作業は **`step1-docker-setup` ディレクトリ**で行います（`make` もここで実行）。

参照: [step1-docker-setup](https://github.com/p0x0q/openclaw-hands-on/tree/main/step1-docker-setup)

#### 2. ディレクトリ構成（目安）

```
step1-docker-setup/
├── docker-compose.yml      # init + gateway の2サービス
├── configs/
│   ├── openclaw.json       # エージェント設定
│   └── agents/             # カスタムエージェント（任意）
├── .env.example
└── Makefile
```

#### 3. 環境変数を用意する

```bash
cp .env.example .env
```

`.env` を編集し、発行した API キーを設定します。

```
ANTHROPIC_API_KEY=sk-ant-api03-（自分のキー）
```

`.env` は Git にコミットしないでください。`.gitignore` で除外されていても、`git status` で追跡されていないことを確認すると安全です。

#### 4. docker-compose の要点（理解用）

- **init コンテナ**: 設定ファイルを共有ボリュームへコピーしてから本体が起動する
- **ポート `18789`**: ゲートウェイ UI + API
- **`restart: unless-stopped`**: 異常終了時の自動復旧

参照: [docker-compose.yml（リポジトリ内）](https://github.com/p0x0q/openclaw-hands-on/blob/main/step1-docker-setup/docker-compose.yml)

#### 5. 起動する

```bash
make start
```

Makefile を使わない場合は `docker compose up`。init が正常終了したあと `openclaw-server` が立ち上がります。

#### 6. 動作確認

**コンテナ状態**

```bash
make status
```

**ヘルスチェック**

```bash
make doctor
```

初回、`Session store dir missing` などの警告が出ることがあります。その場合はコンテナ内で修復します。

```bash
docker exec openclaw-server npx openclaw doctor --fix
```

**ワンショットでチャット**

```bash
make ask MSG="Hello, please respond with a short greeting."
```

ログ確認:

```bash
make logs
```

#### 7. モデルを変更する（任意）

デフォルトは `claude-sonnet-4-5` です。`configs/openclaw.json` の `agents.defaults.model.primary` を編集します（例: `anthropic/claude-opus-4-6`）。

変更を反映するにはボリュームを作り直す例:

```bash
docker compose down -v
make start
```

**注意**: `docker compose down -v` はボリューム削除のため、セッション履歴もリセットされます。

参照: [configs/openclaw.json](https://github.com/p0x0q/openclaw-hands-on/blob/main/step1-docker-setup/configs/openclaw.json)

### Makefile コマンド早見（Docker パターン）

| コマンド | 説明 |
| --- | --- |
| `make start` | コンテナ起動（フォアグラウンド） |
| `make stop` | コンテナ停止 |
| `make logs` | ログ表示 |
| `make status` | コンテナ状態 |
| `make doctor` | ヘルスチェック |
| `make chat` | TUI で対話 |
| `make ask MSG="..."` | ワンショット質問 |

### Docker を使う利点（整理）

| 観点 | ホスト直実行 | Docker |
| --- | --- | --- |
| ファイル操作の影響範囲 | ホスト全体に及びやすい | コンテナ内とマウント先に限定しやすい |
| リソース | ホスト CPU/メモリを直接消費 | `memory` / `cpus` で制限可能 |
| 環境の残り | Node などがホストに残る | コンテナ削除でクリーンしやすい |

---

## パターン B: Docker を使わず構築する（ホストに直接インストール）

Node.js をホストに入れ、`openclaw` CLI とオンボーディングでセットアップする流れです。日本語での応答やワークスペースルールは **`SOUL.md` / `AGENTS.md` 等**で調整します（Qiita 記事の流れ）。

### 前提条件

| 項目 | 要件 |
| --- | --- |
| OS | macOS / Linux / Windows（WSL2 経由など） |
| Node.js | **v22.16 以上**（記事では v24 推奨） |
| LLM | Anthropic / OpenAI / OpenRouter のいずれかの API キー（Ollama ならローカル完結） |

### インストール方法（いずれか）

#### 方法 1: ワンライナー（推奨）

Node.js を含む依存を自動インストールします。

```bash
curl -fsSL https://get.openclaw.ai | bash
```

#### 方法 2: npm / pnpm（グローバル）

```bash
npm install -g openclaw@latest
# または
pnpm add -g openclaw@latest
```

`openclaw` が見つからない場合は、`npm prefix -g` の `bin` を `PATH` に追加します。

```bash
export PATH="$(npm prefix -g)/bin:$PATH"
```

#### 方法 3: ソースからビルド

最新開発版を試す場合。

```bash
git clone https://github.com/openclaw/openclaw.git
cd openclaw
npm install
npm run build
npm start
```

### macOS の注意（ビルド失敗時）

`sharp` のインストールに失敗することがあります。Xcode Command Line Tools を入れてから再試行します。

```bash
xcode-select --install
```

### 初期セットアップ（オンボーディング）

```bash
openclaw onboard --install-daemon
```

ウィザードでは概ね次を設定します。

1. アシスタント名  
2. QuickStart / Advanced  
3. AI プロバイダー（Anthropic / OpenAI / Ollama など）  
4. API キー  
5. モデル選択  
6. チャネル（任意・後からでも可）  
7. Web 検索プロバイダー（任意）  
8. デーモンとして Gateway のインストール・起動  

完了後、設定は通常 **`~/.openclaw/openclaw.json`** に生成されます。Gateway はこのファイルを監視し、保存でホットリロードされる旨が Qiita 記事で説明されています。

チャネルを後回しにする場合は、`openclaw dashboard` で Control UI からチャットするのが手軽です。

### 設定ファイルの要点（非 Docker）

- API キーは平文で書かず **`${ANTHROPIC_API_KEY}`** のように環境変数参照が推奨される例が紹介されています。
- `gateway.port`（例: `18789`）、`agents`、`models.providers`、`channels`、`bindings` などを編集します。

### 日本語環境向けの設定（任意）

OpenClaw 本体に「日本語ロケール」スイッチはない一方、エージェントの振る舞いはワークスペース内の Markdown で調整できます。

| ファイル | 役割の例 |
| --- | --- |
| `SOUL.md` | 人格・言語（日本語で応答する等） |
| `AGENTS.md` | 行動ルール（ファイル操作の確認、コマンド前の説明など） |
| `IDENTITY.md` | 表示名・アイコンなど |

配置先の例: デフォルトワークスペース `~/.openclaw/agents/main/` など（記事の例に合わせる）。

反映のため Gateway 再起動や新規セッションが案内されています。

```bash
openclaw gateway restart
openclaw dashboard
```

### 基本操作（非 Docker）

| 用途 | コマンド例 |
| --- | --- |
| Control UI（ブラウザ） | `openclaw dashboard` → `http://localhost:18789` 付近 |
| 対話チャット | `openclaw chat` |
| 単発実行 | `openclaw run "今日の天気を調べて"` |

### よくあるトラブル（非 Docker）

- **Gateway が起動しない**: `openclaw gateway logs` / `openclaw config validate` で設定スキーマを確認  
- **API キーエラー**: `echo $ANTHROPIC_API_KEY` などで環境変数を確認  
- **Node が古い**: `node -v` で v22.16 未満なら更新（nvm 利用例も記事にあり）  
- **sharp エラー（macOS）**: `xcode-select --install` や `node-gyp` など  

---

## 2 パターンの選び方（目安）

| 観点 | Docker（パターン A） | ホスト直（パターン B） |
| --- | --- | --- |
| ホストへの Node 要件 | 不要（イメージに同梱） | Node.js 必須 |
| 被害範囲の限定 | コンテナ・マウントで切りやすい | ホストに直接影響しやすい |
| 設定の触り方 | `configs/` と compose が中心 | `~/.openclaw/` と CLI が中心 |
| 日本語人格・ルール | コンテナ内の設定・ボリュームに合わせて編集 | `SOUL.md` 等でそのまま試しやすい |

---

## 参考リンク（公式・関連）

- [OpenClaw（GitHub）](https://github.com/openclaw/openclaw)
- [OpenClaw ドキュメント](https://docs.openclaw.ai/)
- [OpenClaw ドキュメント（日本語）](https://docs.openclaw.ai/ja-JP)

Qiita 記事では NemoClaw（エンタープライズ向け拡張）なども触れられています。詳細は各記事・公式ドキュメントを参照してください。
