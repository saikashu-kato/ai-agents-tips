# X（旧 Twitter）API 利用手順メモ

## 参考元について

実践寄りの手順・コード例は次の Qiita 記事を参考にしています（投稿メイン・無料プラン・TypeScript など）。

- [X（旧Twitter） API入門 〜 自動投稿でラクしよう](https://qiita.com/sky2432/items/9a89443e08d6b77b4687)（@sky2432）

仕様の正確な上限・エンドポイント仕様は **必ず公式**で確認してください。

- [X API の概要（Introduction）](https://docs.x.com/x-api/introduction)
- [X Developer Platform（ポータル）](https://developer.x.com/)
- [Manage Posts の概要](https://developer.x.com/en/docs/twitter-api/tweets/manage-tweets/introduction)
- [Manage Posts クイックスタート](https://developer.x.com/en/docs/twitter-api/tweets/manage-tweets/quick-start)
- [X API v2 の認証とエンドポイントの対応](https://docs.x.com/fundamentals/authentication/guides/v2-authentication-mapping)
- [Tools and libraries（公式が案内する SDK / ライブラリ）](https://docs.x.com/x-api/tools-and-libraries/overview)

トークン管理の考え方（OAuth 2.0 のリフレッシュの使い捨て性質など）の補足として、次の解説も参照できます（**非公式・比喩入りの入門**。数値・挙動は必ず公式で確認してください）。

- [X API OAuth 2.0トークン管理入門｜使い捨てリフレッシュトークンの仕組みを図解](https://tech-lab.sios.jp/archives/49895)（SIOS Tech Lab）

---

## プランの目安（記事・無料利用時の例）

※ **料金・上限は変更されるため、契約画面と [X API Introduction](https://docs.x.com/x-api/introduction) で必ず最新を確認**してください。

Qiita 記事では **投稿中心なら無料プランで足りる**旨と、当時の例として次のような上限が紹介されています。

| 項目 | 記事にあった例（参考） |
| --- | --- |
| 読み取り | 100 回／月 |
| 投稿 | 500 回／月 |

有料プランは高額になりやすいため、まず無料で要件を満たせるか検討する、という整理が記事でも述べられています。

---

## 1. 環境構築の流れ（開発者アカウント〜キー取得）

### 1.1 開発者アカウントの利用申請（無料プラン含む）

1. [developer.x.com](https://developer.x.com/) にアクセスし、X アカウントでサインインする。
2. ポータルでは Basic / Pro など有料が目立つことがあります。**無料で始める場合**は、申請ページ下部などに **「Sign up for Free Account」** があるので、そこから無料プランの登録を進められます（手順の一例として Qiita では次の URL が紹介されています）。

   - https://developer.x.com/en/portal/petition/essential/basic-info

3. **「X のデータと API のすべてのユースケースを説明してください」** などのフォームでは、利用目的を **一定文字数以上**（記事では **250 文字以上**）入力する必要がある場合があります。記事では、用途を英語で生成してもらうのに生成 AI を使うのがよい、とされています。
4. 記事作者の環境では **すぐに API が使える状態になった**とのことですが、審査の有無・所要時間はポリシーや時期で変わります。

### 1.2 プロジェクトとアプリの作成

1. Developer Portal で **Project** を作成する。
2. 同一プロジェクト内に **Developer App** を作成する（API のキー・トークンはアプリ単位で管理される）。

### 1.3 アプリ権限（投稿に必要）

投稿（ポスト作成）には **書き込み権限**が必要です。

Developer Portal で **対象 App を選択**し、**Settings → User authentication settings** を開きます。Qiita 記事では **App permissions を Read and write に変更して保存**する手順が紹介されています。

- アプリの権限を **Read and Write**（読み取り＋書き込み）に設定する。
- **権限を変更した場合は、Access Token / Access Token Secret を再生成**する必要があることが多い（古いトークンのままでは投稿が失敗する）。

### 1.4 認証方式の有効化

用途に応じて Developer Portal で以下を有効化・設定する。

- **OAuth 1.0a**：サーバー側やバッチでユーザーコンテキストとして投稿する場合によく使う。
- **OAuth 2.0（Authorization Code with PKCE）**：ユーザーにブラウザで同意してもらい、刷新トークンと組み合わせる Web アプリ向け。

投稿系エンドポイントは **ユーザーコンテキスト**が必須で、**App のみの Bearer トークン（OAuth 2.0 Client Credentials）では投稿できません**。認証とエンドポイントの対応は [v2 authentication mapping](https://docs.x.com/fundamentals/authentication/guides/v2-authentication-mapping) を参照してください。

### 1.5 キー・トークンの取得（Keys and tokens）

アプリの **Keys and tokens** 画面で、用途に応じて生成・コピーします。

| 種類 | 用途の例 |
| --- | --- |
| API Key / API Secret（Consumer Key / Secret） | OAuth 1.0a の署名に使用 |
| Access Token / Access Token Secret | ユーザーの OAuth 1.0a ユーザーコンテキスト |
| Client ID / Client Secret（OAuth 2.0） | PKCE フロー |
| Bearer Token（OAuth 2.0 App の場合） | **投稿には使えない**（読み取り専用エンドポイント向けなど） |

Qiita 記事では、投稿用に **API Key / Secret と Access Token / Secret の 4 つ**を `.env` に用意する例が示されています（変数名は一例です）。

```bash
TWITTER_API_KEY=
TWITTER_API_SECRET=
TWITTER_ACCESS_TOKEN=
TWITTER_ACCESS_TOKEN_SECRET=
```

OAuth 2.0 の **Client ID / Client Secret** は、PKCE など別フローで使います。サーバーから自分のアカウントで投稿するだけなら、記事のように **OAuth 1.0a のユーザートークン 4 点**で足りることが多いです。

秘密情報はリポジトリにコミットせず、環境変数やシークレット管理に保存する。

### 1.6 簡単な動作確認（Bearer Token でユーザー情報を読む）

投稿前に **読み取りが通るか**確かめる例です。**Projects & Apps → 対象 App → Keys and tokens** で **Bearer Token** を生成し、API クライアント（Postman 等）の Authorization で **Bearer Token** を指定します。

ユーザー名からユーザー情報を取得する例（パスの `:user` にユーザー名を入れる）:

```http
GET https://api.x.com/2/users/by/username/:user
```

※ ホストは環境により **`api.twitter.com`** の表記も使われます。公式ドキュメントのベース URL に合わせてください。

---

## 1.7 補足: トークンの扱い（OAuth 1.0a と OAuth 2.0）

X API では **認証方式ごとに「何を保存し、いつ取り直すか」がまったく違う**ため、混同しやすいです。ここでは本書で触れている **OAuth 1.0a（ユーザートークン 4 点）**と、Web アプリ等でよく使う **OAuth 2.0（Authorization Code + PKCE）**を分けて整理します。OAuth 2.0 のリフレッシュまわりの直感的な説明は [SIOS Tech Lab の記事](https://tech-lab.sios.jp/archives/49895) がわかりやすいです（以下の **有効期限やスコープ名は記事・過去情報に基づく例**であり、変更され得ます。**必ず最新の X 公式ドキュメントで確認**してください）。

### OAuth 1.0a ユーザーコンテキスト（本書の Qiita 例）

ポータルで発行する **API Key / Secret + Access Token / Secret** をそのまま長期利用するパターンです。

| 観点 | 内容 |
| --- | --- |
| 用途のイメージ | 自分のボット・cron・サーバーから **固定アカウントで投稿**する |
| 期限のイメージ | アクセストークンは **発行し直すまで有効**（アプリ連携を解除する・権限変更・再生成するまで使える、という運用が一般的） |
| 取り直しが必要なタイミングの例 | App の権限を **Read and write** に変えた直後、漏えいが疑われたとき、など |

**OAuth 2.0 の「2 時間で切れるアクセストークン」とは別物**です。こちらは「毎回リフレッシュする」より **`.env` に 4 点を置いて署名する**構成が多いです。

### OAuth 2.0（PKCE）とアクセストークン / リフレッシュトークン

ブラウザでユーザーにログインしてもらい、得たトークンで API を叩く方式です [認証マッピング](https://docs.x.com/fundamentals/authentication/guides/v2-authentication-mapping)。SIOS Tech Lab の記事では、次のような整理がされています。

| トークン | 役割（記事の比喩） | 記事にあった目安 |
| --- | --- | --- |
| **アクセストークン** | 実際に `GET/POST` に載せる短期チケット | 約 **2 時間**（7200 秒） |
| **リフレッシュトークン** | アクセストークンを **ユーザー再ログインなしで**取り直すための長期トークン | 約 **6 か月** |

**リフレッシュトークンを発行してもらうには**、認可リクエストで **`offline.access` スコープ**を含める必要がある、と記事では強調されています。これがないとアクセストークン失効のたびに、ユーザーに再度ログインしてもらう必要が出ます。

### 最重要: リフレッシュトークンの「使い捨て（ローテーション）」

X の OAuth 2.0 実装では、（一般的な他サービスと比べても）**リフレッシュのたびに古いリフレッシュトークンが無効になる**扱いになることがある、と [SIOS の解説](https://tech-lab.sios.jp/archives/49895) では述べられています。

1. リフレッシュトークン A でトークン更新 API を呼ぶ。  
2. 応答として **新しいアクセストークン** と **新しいリフレッシュトークン B** が返る。  
3. **A はその時点で使えなくなる**（あとから同じ A を再送するとエラーになり得る）。  
4. **次回の更新では必ず B を使う** — B も 1 回使ったら無効化され、また新しいペアが返る、という **ローテーション**を繰り返す。

**実装でよくある誤り（記事より）**

- リフレッシュトークンは **何度でも使える**と思って保存し続ける → **誤り**。最新のトークンだけを保持する。  
- 更新レスポンスで **アクセストークンだけ**保存し、**新しいリフレッシュトークンを捨てる** → **次回の更新で失敗**しやすい。  
- **トークンは永続で無効にならない**と思う → アクセストークンは短期、リフレッシュも **約 6 か月で期限**が来る、など期限設計が必要。

### トークンの保存先の考え方（記事の整理）

| 保存先 | 向いている例 | メモ |
| --- | --- | --- |
| **クッキー等（ブラウザ側）** | ユーザーがその場で「投稿」ボタンを押すアプリ | 短期・実装は単純になりやすい。HttpOnly などの扱いに注意 |
| **データベース（サーバー側）** | **cron・予約投稿・バッチ**など、ユーザー操作と非同期の自動投稿 | 長期保管が必要。**平文保存は避け、暗号化**やアクセス制御を検討する |

自動投稿バッチでは、実行直前にアクセストークンが切れていないか、必要ならリフレッシュしてから投稿する、といった **更新フロー設計**が重要になります。

---

## 2. 自動投稿で使う API の種類と使い方

### 2.1 テキスト投稿の本体（必須）：Manage Posts（v2）

**ポストを作成・削除する**エンドポイント群です。自動投稿の中心になります。

| 操作 | メソッドとパス（v2） | 認証 |
| --- | --- | --- |
| ポストする | `POST https://api.twitter.com/2/tweets`（同一 API は `https://api.x.com/2/tweets` でも利用されることがあります） | OAuth 1.0a ユーザーコンテキスト **または** OAuth 2.0（Authorization Code + PKCE、適切なスコープ） |
| ポストを削除 | `DELETE https://api.twitter.com/2/tweets/:id` | 同上 |

**リクエストボディ例（テキストのみ）**

```json
{
  "text": "自動投稿の本文（長さ・改行はポリシーと上限に従う）"
}
```

**投稿に使える主なオプション（概要）**

[Manage Posts の概要](https://developer.x.com/en/docs/twitter-api/tweets/manage-tweets/introduction) では、`POST` で次のような拡張が可能です（パラメータは公式リファレンスで確認）。

- リプライ（返信先ポスト ID の指定）
- 引用ポスト
- アンケート（poll）
- リプライ制限（reply 設定）
- **メディア付き**（後述の `media_ids`）
- 位置情報・特定ユーザーへのタグなど（利用プラン・権限による）

**レート制限（参考）**

公式の Manage Posts 説明では、ユーザ単位で **POST は 15 分あたり 200 リクエスト**、**DELETE は 15 分あたり 50** などが述べられています。さらに **3 時間あたり 300 リクエスト**にマネージポスト／リツイート操作が含まれる旨の記載があります。最新値は API のレスポンスヘッダとドキュメントで確認してください。

---

### 2.2 画像・動画を付ける場合：メディアアップロード（v1.1）＋投稿（v2）

テキストだけでなく **画像・GIF・動画**を付ける場合は、多くのケースで次の二段階になります。

1. **メディアをアップロード**して `media_id`（または `media_id_string`）を取得する。  
   - 画像の単純アップロード: [POST media/upload](https://developer.x.com/en/docs/x-api/v1/media/upload-media/api-reference/post-media-upload)（`upload.twitter.com`）  
   - 動画・大きいファイル: **チャンク分割アップロード**（INIT → APPEND → FINALIZE）。概要は [Media upload overview](https://developer.x.com/en/docs/x-api/v1/media/upload-media/overview)。
2. **`POST /2/tweets`** の本文に **`media.media_ids`** を指定してポストする（複数枚対応の場合あり）。

メディアには **有効期限**（返却される `expires_after_secs`）があるため、アップロードから投稿までの時間が空きすぎないよう注意します。

任意で画像の alt テキストを付ける場合は [POST media/metadata/create](https://developer.x.com/en/docs/x-api/v1/media/upload-media/api-reference/post-media-metadata-create) などを参照します。

**本文付きメディア投稿のイメージ（フィールド名はリファレンスで確定すること）**

```json
{
  "text": "画像付きの文案",
  "media": {
    "media_ids": ["取得したmedia_id_string"]
  }
}
```

---

### 2.3 投稿後の確認・監視に使える API（任意）

| 用途 | 例 | メモ |
| --- | --- | --- |
| 単一ポストの取得 | `GET /2/tweets/:id` | 投稿結果の ID で内容確認 |
| 複数 ID の一括取得 | `GET /2/tweets` | `ids` クエリで複数取得 |
| ユーザー情報 | Users 系の v2 エンドポイント | 表示名・ユーザー名の解決など |

認証方式はエンドポイントごとに **ユーザーコンテキスト / App のみ**が異なります。[認証マッピング表](https://docs.x.com/fundamentals/authentication/guides/v2-authentication-mapping) で確認します。

---

### 2.4 OAuth 1.0a での投稿イメージ（サーバー・バッチ）

[Manage Posts クイックスタート](https://developer.x.com/en/docs/twitter-api/tweets/manage-tweets/quick-start) では Postman を例に、**OAuth 1.0a User Context** で `POST /2/tweets` に JSON を送る手順が説明されています。

プログラムからは、リクエストごとに **OAuth 1.0a の署名付き Authorization ヘッダ**を生成し、`Content-Type: application/json` で本文を送ります（ライブラリ例: 各言語の OAuth 1.0a 実装）。

### 2.5 TypeScript 例（`twitter-api-v2`）

Qiita 記事では [twitter-api-v2](https://www.npmjs.com/package/twitter-api-v2) を使い、cron 等のスケジューラと組み合わせて定期投稿する想定が紹介されています。

```typescript
import { TwitterApi } from "twitter-api-v2";

const client = new TwitterApi({
  accessSecret: env.TWITTER_ACCESS_TOKEN_SECRET,
  accessToken: env.TWITTER_ACCESS_TOKEN,
  appKey: env.TWITTER_API_KEY,
  appSecret: env.TWITTER_API_SECRET,
}).readWrite;

await client.v2.tweet({
  text: "ツイート内容",
});
```

**画像を添付する例**

```typescript
const mediaId = await client.v1.uploadMedia("./image.png");

const tweet = await client.v2.tweet({
  text: "ツイート内容",
  media: {
    media_ids: [mediaId],
  },
});
```

**リプライの例**

```typescript
const tweet = await client.v2.tweet({
  text: "ツイート内容",
});

await client.v2.tweet({
  text: "リプライツイート内容",
  reply: {
    in_reply_to_tweet_id: tweet.data.id,
  },
});
```

ライブラリの詳細は次を参照してください。

- [node-twitter-api-v2 / v1 ドキュメント](https://github.com/plhery/node-twitter-api-v2/blob/master/doc/v1.md)
- [node-twitter-api-v2 / v2 ドキュメント](https://github.com/plhery/node-twitter-api-v2/blob/master/doc/v2.md)

---

## 3. 運用上の注意

- 参考記事では、**夜間の定時に新規コンテンツ紹介を投稿する**など、**cron 等のスケジューラと API を組み合わせる**用途が想定されています（スケジューラ本体の作り方は記事外）。
- **利用プラン（無料／有料）**により、月間の投稿数や利用可能エンドポイントが制限されます。数値は変更されるため、契約内容と Developer Portal の表示で確認します。
- 自動化・ボット運用は [X の自動化に関するルール](https://help.x.com/ja/rules-and-policies/twitter-automation) および開発者契約に従います。
- v1.1 の `statuses/update` など旧エンドポイントは方針により非推奨・段階廃止の対象になり得るため、**新規開発は v2（`/2/tweets`）を優先**します。

---

## 4. 公式リンク一覧（随時アップデート確認）

| 内容 | URL |
| --- | --- |
| X API Introduction | https://docs.x.com/x-api/introduction |
| 無料申請フロー（一例・記事で紹介） | https://developer.x.com/en/portal/petition/essential/basic-info |
| 開発者ポータル | https://developer.x.com/ |
| Manage Posts 概要 | https://developer.x.com/en/docs/twitter-api/tweets/manage-tweets/introduction |
| Manage Posts クイックスタート | https://developer.x.com/en/docs/twitter-api/tweets/manage-tweets/quick-start |
| v2 認証とエンドポイント対応 | https://docs.x.com/fundamentals/authentication/guides/v2-authentication-mapping |
| Tools and libraries | https://docs.x.com/x-api/tools-and-libraries/overview |
| npm `twitter-api-v2` | https://www.npmjs.com/package/twitter-api-v2 |
| メディアアップロード（simple） | https://developer.x.com/en/docs/x-api/v1/media/upload-media/api-reference/post-media-upload |
| メディアアップロード概要 | https://developer.x.com/en/docs/x-api/v1/media/upload-media/overview |

### 参考記事（非公式・実践向け）

| 内容 | URL |
| --- | --- |
| X API 入門・自動投稿（Qiita） | https://qiita.com/sky2432/items/9a89443e08d6b77b4687 |
| OAuth 2.0 トークン管理・リフレッシュの使い捨て（SIOS Tech Lab） | https://tech-lab.sios.jp/archives/49895 |
