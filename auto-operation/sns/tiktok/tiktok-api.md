# TikTok APIメモ

## 1. コンテンツ（ショート動画）を探す / 絞り込む / ソートする

- TikTok Research APIで取得可能。

### エンドポイント

```http
POST https://open.tiktokapis.com/v2/research/video/query/
Authorization: Bearer {access_token}
Content-Type: application/json
```

### リクエスト例

```json
{
  "query": {
    "and": [
      {
        "operation": "IN",
        "field_name": "hashtag_name",
        "field_values": ["dance", "japan"]
      }
    ]
  },
  "max_count": 50,
  "cursor": 0
}
```

### 公式リファレンス

- https://developers.tiktok.com/doc/research-api-specs-query-videos

---

## 2. コンテンツ（動画）を投稿する

- Content Posting APIで動画投稿が可能。

### 手順

1. 動画アップロードの初期化

```http
POST /v2/post/publish/inbox/video/init/
Authorization: Bearer {access_token}
Content-Type: application/json
```

```json
{
  "source_info": {
    "source": "FILE_UPLOAD"
  },
  "video_size": "{ファイルサイズ}",
  "chunk_size": "{チャンクサイズ}",
  "total_chunk_count": "{分割数}"
}
```

2. 動画チャンクアップロード

```http
PUT {upload_url}
Content-Type: video/mp4
Content-Range: bytes {開始}-{終了}/{合計}
{バイナリデータ}
```

### 公式リファレンス

- https://developers.tiktok.com/doc/content-posting-api-reference-upload-video/
