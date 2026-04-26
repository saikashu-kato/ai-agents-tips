# CursorでCodexのAPI Key設定方法

## 概要

Cursor IDEでCodexを使用するために必要なAPI keyの設定手順を説明します。CodexはOpenAIのAPIを使用するため、OpenAIのAPI keyが必要です。

## 前提条件

- Cursor IDEがインストール済み
- OpenAIアカウント（ChatGPT Plusプラン推奨）
- インターネット接続

---

## ステップ1: OpenAI API Keyの取得

### 1.1 OpenAIプラットフォームにアクセス
1. [OpenAI API Keys管理ページ](https://platform.openai.com/api-keys) にアクセス
2. OpenAIアカウントでログイン

### 1.2 API Keyの作成
1. 「Create new secret key」ボタンをクリック
2. キーに名前を付ける（例：「Cursor Codex」）
3. 生成されたAPI keyをコピー
4. **重要**: API keyは一度しか表示されないため、安全な場所に保存

### 1.3 API Keyの管理
- API keyは機密情報です
- 他人と共有しないでください
- 定期的にローテーションすることを推奨
- 不要になったら削除してください

---

## ステップ2: CursorでのAPI Key設定

### 2.1 設定画面を開く
**方法1: 設定アイコンから**
1. Cursorを起動
2. 画面右上の歯車アイコン（⚙️）をクリック

**方法2: ショートカットキー**
1. `Cmd + ,` (macOS) または `Ctrl + ,` (Windows/Linux) を押す

### 2.2 Models設定に移動
1. 左側のナビゲーションメニューから「Models」を選択
2. 「Custom API Keys」セクションを探す

### 2.3 API Keyを入力
1. 「OpenAI API Key」の入力欄に取得したAPI keyを貼り付け
2. 入力が完了したら、入力欄の外をクリック

### 2.4 接続をテスト
1. 「Verify」ボタンをクリック
2. 接続が成功すると「✓ Verified」と表示される
3. エラーが表示された場合は、API keyを再確認

### 2.5 設定を保存
1. 検証が成功したら、設定は自動的に保存される
2. 設定画面を閉じる

---

## ステップ3: Codexの使用開始

### 3.1 Codexウィンドウを開く
1. `Ctrl+Shift+P` (Windows/Linux) または `Cmd+Shift+P` (macOS) でコマンドパレットを開く
2. "Open Codex" と入力して選択
3. または、ファイルを開いている状態で直接アクセス可能

### 3.2 基本的な使用方法
1. コードを選択
2. Codexウィンドウで指示を入力
3. 提案されたコードを確認
4. 必要に応じて適用

---

## トラブルシューティング

### よくある問題と解決方法

#### 1. API Keyが認識されない
**症状**: 「Invalid API key」エラーが表示される

**解決方法**:
- API keyを再確認
- コピー&ペースト時に余分な空白が含まれていないか確認
- OpenAIプラットフォームでAPI keyが有効か確認

#### 2. 接続エラー
**症状**: 「Connection failed」エラーが表示される

**解決方法**:
- インターネット接続を確認
- ファイアウォール設定を確認
- OpenAIのサービス状況を確認

#### 3. 認証エラー
**症状**: 「Authentication failed」エラーが表示される

**解決方法**:
- OpenAIアカウントの状態を確認
- API keyの権限を確認
- 新しいAPI keyを生成して再設定

#### 4. Codexウィンドウが開かない
**症状**: コマンドパレットで「Open Codex」が見つからない

**解決方法**:
- Cursorを再起動
- 拡張機能が正しくインストールされているか確認
- Cursorのバージョンを最新に更新

---

## セキュリティのベストプラクティス

### API Keyの保護
1. **環境変数での管理**（推奨）
   ```bash
   export OPENAI_API_KEY="your-api-key-here"
   ```

2. **定期的なローテーション**
   - 月1回程度でAPI keyを更新
   - 古いAPI keyは削除

3. **アクセス制限**
   - 必要最小限の権限のみ付与
   - 使用状況を定期的に監視

### 設定ファイルの管理
- 設定ファイルにAPI keyを直接記述しない
- バージョン管理システムにAPI keyを含めない
- チーム共有時は環境変数を使用

---

## 高度な設定

### カスタム設定
1. **モデル選択**: 使用するOpenAIモデルを選択
2. **温度設定**: 生成されるコードの創造性を調整
3. **最大トークン数**: 生成されるコードの長さを制限

### プロキシ設定
企業環境でプロキシを使用する場合：
1. 設定画面で「Proxy」セクションを探す
2. プロキシサーバーの情報を入力
3. 認証が必要な場合は認証情報も設定

---

## 参考リンク

- [OpenAI API Keys管理](https://platform.openai.com/api-keys)
- [Cursor公式ドキュメント](https://docs.cursor.com/ja/settings/api-keys)
- [OpenAI API Documentation](https://platform.openai.com/docs)
- [Cursor Community](https://cursor.com/community)

---

## まとめ

CursorでCodexを使用するには、OpenAIのAPI keyが必要です。上記の手順に従って設定することで、Cursor内でCodexの強力なAI機能を活用できるようになります。

**重要なポイント**:
- API keyは機密情報として適切に管理する
- 定期的にローテーションする
- セキュリティのベストプラクティスに従う
- 問題が発生した場合はトラブルシューティングを参照

適切な設定により、Codexは開発効率を大幅に向上させる強力なツールとなります。
