# Instagram APIメモ

## 1. コンテンツ（画像・リール）を探す / 絞り込む / ソートする

- 取得起点は基本的にハッシュタグ。
- API自体に十分な絞り込み・ソート機能はないため、取得後にアプリ側でフィルタ・並び替えを行う想定。

### 手順

1. ハッシュタグIDを取得

```http
GET /ig_hashtag_search
?user_id={ig-business-user-id}
&q=cats
```

2. ハッシュタグIDから投稿を取得

```http
GET /{hashtag-id}/top_media?user_id={ig-business-user-id}&fields={フィールド}
GET /{hashtag-id}/recent_media?user_id={ig-business-user-id}&fields={フィールド}
```

- `fields` には `id`, `media_type`, `media_url`, `permalink`, `caption`, `timestamp` などを指定可能。

### 公式リファレンス

- https://developers.facebook.com/docs/instagram-platform/instagram-graph-api/reference/ig-hashtag-search?locale=ja_JP
- https://developers.facebook.com/docs/instagram-platform/instagram-graph-api/reference/ig-hashtag/top-media?locale=ja_JP
- https://developers.facebook.com/docs/instagram-platform/instagram-graph-api/reference/ig-hashtag/recent-media?locale=ja_JP

---

## 2. コンテンツ（画像・リール）を投稿する

- 確認した限り、外部URL指定でメディアを渡す方式。
- 画像か動画かは `media_type` で指定。

### 手順

1. メディアを指定

```http
POST /{ig-user-id}/media
Content-Type: application/json
Authorization: Bearer {access-token}
```

```json
{
  "media_type": "IMAGE",
  "image_url": "https://example.com/photo.jpg",
  "caption": "投稿文字列"
}
```

2. 投稿

```http
POST /{ig-user-id}/media_publish
```

```json
{
  "creation_id": "{先ほど取得したID}"
}
```

### 公式リファレンス

- https://developers.facebook.com/docs/instagram-platform/instagram-graph-api/reference/ig-user/media
- https://developers.facebook.com/docs/instagram-platform/instagram-graph-api/reference/ig-user/media_publish
