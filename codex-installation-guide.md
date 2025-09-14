# Codex インストール手順ガイド

## 概要

このガイドでは、OpenAI Codexの2つの主要なインストール方法について説明します：
- **Cursor版**: IDE拡張機能としてのCodex
- **CLI版**: コマンドライン版のCodex

## 前提条件

- **ChatGPT Plusプラン**が必要（両方の方法で共通）
- **Node.js**がインストール済み（CLI版の場合）
- **Git**がインストール済み（推奨）

---

## 1. Cursor版 Codex（拡張機能）

### 環境要件
- Windows 11
- WSL2 (Ubuntu)
- Cursor IDE
- ChatGPT Plusプラン

### インストール手順

#### ステップ1: 拡張機能のダウンロード
1. [Codex IDEページ](https://developers.openai.com/codex/ide) にアクセス
2. 拡張機能をダウンロード
   - 2025年8月29日時点では、MarketPlaceより直接ダウンロードが確実
   - 最新の情報は公式ページで確認してください

#### ステップ2: Cursorでの設定
1. Cursor IDEを開く
2. `Ctrl+Shift+P` でコマンドパレットを開く
3. "Open Codex" と入力してCodexウィンドウを開く
   - または、ファイルを開いている状態で直接アクセス可能

#### ステップ3: ChatGPTにログイン
1. 表示されたログイン用ウェブページでChatGPTにログイン
2. ログイン後、自動的にCursorに戻る

#### ステップ4: チュートリアル完了
1. Codexウィンドウで "Next" を押してチュートリアルを進める
2. 設定完了

### 使用方法
- Cursor内でコードを選択
- Codexウィンドウで指示を入力
- 提案されたコードを確認・適用

---

## 2. CLI版 Codex

### インストール方法

#### 方法1: npm経由（推奨）
```bash
npm i -g @openai/codex
```

#### 方法2: Homebrew経由（macOS）
```bash
brew install codex
```

### 初期設定

#### ステップ1: 認証設定
1. 初回実行時に認証プロセスが開始される
2. ChatGPT Plusプランでログイン
3. 認証完了後、ローカルで実行可能

#### ステップ2: 実行モードの選択
Codex CLIには3つの実行モードがあります：

| モード | 説明 | 安全性 | 用途 |
|--------|------|--------|------|
| **Suggest** | 提案のみ | 最高 | 学習・確認 |
| **Auto Edit** | 編集提案を自動承認 | 中 | 日常的な作業 |
| **Full Auto** | 完全自動実行 | 低 | 高度な自動化 |

### 基本的な使用方法

#### Suggestモード（推奨）
```bash
# プロジェクトの説明を追加
codex --suggest "プロジェクトの説明を追加して"

# 特定のファイルを編集
codex --suggest "READMEファイルを更新して" --file README.md
```

#### Auto Editモード
```bash
# 編集提案を自動承認
codex --auto-edit "バグを修正して"

# 複数ファイルを対象
codex --auto-edit "コードの品質を改善して" --file "src/**/*.js"
```

#### Full Autoモード（注意が必要）
```bash
# 完全自動実行（慎重に使用）
codex --full-auto "テストを追加して"

# プロジェクト全体を対象
codex --full-auto "リファクタリングを実行して"
```

### セキュリティ機能

#### OSレベルのサンドボックス
- 実行環境を隔離
- システムへの影響を最小化
- 安全なテスト環境を提供

#### データプライバシー
- ローカル実行でデータが外部に送信されない
- 機密情報の保護
- 企業環境での使用に適している

#### 承認ワークフロー
- 3段階の安全レベル
- 段階的な自動化レベル
- リスク管理の最適化

### 推奨設定

#### 重要な作業前の準備
```bash
# 新しいブランチを作成
git checkout -b codex-experiment

# 現在の状態をコミット
git commit -am "Before Codex session"
```

#### 定期的なバックアップ
```bash
# セッション中の中間コミット
git commit -am "Codex session checkpoint"

# 作業完了後のコミット
git commit -am "Codex session completed"
```

---

## 3. 比較表

| 項目 | Cursor版 | CLI版 |
|------|----------|-------|
| **実行環境** | Cursor IDE内 | ターミナル |
| **インストール** | 拡張機能 | npm/Homebrew |
| **操作** | GUI中心 | コマンドライン |
| **セキュリティ** | IDE内実行 | OSレベルサンドボックス |
| **用途** | コード編集支援 | 自動化・CI/CD |
| **学習コスト** | 低（IDEユーザー向け） | 中（CLI操作が必要） |
| **自動化** | 限定的 | 高度 |
| **チーム開発** | 個人向け | チーム向け |

---

## 4. 推奨事項

### 初心者向け
1. **Cursor版から始める**
   - 直感的な操作
   - IDE内での統合
   - 学習コストが低い

### 自動化重視
1. **CLI版を選択**
   - 高度な自動化機能
   - CI/CDとの統合
   - スクリプト化可能

### セキュリティ重視
1. **CLI版のSuggestモードから開始**
   - 最高レベルの安全性
   - 段階的な学習
   - リスクの最小化

### チーム開発
1. **両方を併用**
   - 用途に応じて使い分け
   - 個人作業: Cursor版
   - 自動化: CLI版

---

## 5. トラブルシューティング

### よくある問題

#### 認証エラー
```bash
# 認証情報をクリア
codex logout
codex login
```

#### 権限エラー
```bash
# グローバルインストールの確認
npm list -g @openai/codex

# 権限の修正
sudo npm i -g @openai/codex
```

#### 実行エラー
```bash
# バージョンの確認
codex --version

# 最新版への更新
npm update -g @openai/codex
```

### サポート

- **公式ドキュメント**: [OpenAI Codex Documentation](https://help.openai.com/)
- **GitHub Issues**: [Codex CLI Issues](https://github.com/openai/codex/issues)
- **コミュニティ**: [OpenAI Community](https://community.openai.com/)

---

## 6. まとめ

Codexは、開発ワークフローを大幅に効率化する強力なツールです。Cursor版とCLI版の両方とも、ChatGPT Plusプランが必要で、ローカル実行による高いセキュリティを提供しています。

**推奨アプローチ:**
1. 初心者はCursor版から開始
2. 自動化が必要な場合はCLI版を追加
3. セキュリティを重視する場合はSuggestモードから開始
4. チーム開発では両方を併用

適切な設定と使用方法により、Codexは開発効率を大幅に向上させるでしょう。

---

## 参考リンク

- [Codex IDE公式ページ](https://developers.openai.com/codex/ide)
- [OpenAI Codex CLI完全ガイド](https://smartscope.blog/generative-ai/chatgpt/openai-codex-cli-comprehensive-guide/)
- [CursorでCodex拡張機能を使う方法](https://qiita.com/bearjiro/items/b1393df8d98422e6678a)
- [Homebrew Formula（codex）](https://formulae.brew.sh/formula/codex)
- [OpenAI Help Center](https://help.openai.com/)
