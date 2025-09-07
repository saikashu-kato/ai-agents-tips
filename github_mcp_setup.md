# Github MCP セットアップガイド

## 前提条件

- Node.jsとnpmがインストールされていること
- Githubでtokenを作成していること

## 1. Cursor での設定

### 1.1 設定ファイルの作成

(Macの場合)メニューから Cursor > 基本設定 > Cursor Settings を選択し、
開いたCursor Settingsのウィンドウ左側にあるMCPの項目を選択します。
MCPの項目を選択後「+ Add new global MCP server」ボタンを押下し次の設定ファイルの内容を記載します。
MCP名の左側に表示されているドットが緑色になったら、設定成功。

### 1.2 設定ファイルの内容

```mcp.json
{
  "mcpServers": {
    "playwright-mcp": {
      "command": "npx",
      "args": [
        "@playwright/mcp@latest"
      ]
    },
    "github": {
      "command": "npx",
      "args": [
        "-y",
        "@modelcontextprotocol/server-github"
      ],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": ""
      }
    }
  }
}
```

## 2. Claude Desktop での設定

### 2.1 PlaywrightとPlaywright MCPをインストール

以下のコマンドでGithub MCPをインストールする。(不要の可能性あり)
```
npm install -g @modelcontextprotocol/server-github
```

### 2.2 Claude DesktopにMCPを設定

(Macの場合)メニューから Claude > 設定... を選択し、設定画面が開いたら「開発者」タブを選択。
「設定の構成」ボタンを押下するとFinderが開くため、claude_desktop_config.jsonを探して編集する。
「開発者」タブにGithubが追加され、ステータスがrunningになっていれば設定成功。

### 2.3 設定ファイルの内容
githubの項目を追加。他のMCPを追加したい場合はfigma-developer-mcpのように記載する。

```claude_desktop_config.json
{
  "mcpServers": {
    "playwright": {
      "command": "npx",
      "args": [
        "@playwright/mcp@latest"
      ]
    },
    "figma-developer-mcp": {
      "command": "npx",
      "args": ["-y", "figma-developer-mcp", "--stdio"],
      "env": {
        "FIGMA_API_KEY": ""
      }
    },
    "github": {
      "command": "npx",
      "args": [
        "-y",
        "@modelcontextprotocol/server-github"
      ],
      "env": {
        "YOUR_TOKEN"
      }
    }
  }
}
```

## 3. トラブルシューティング

**問題1: Claude Desktopでnpxが認識されない**
Node.jsをasdf(homebrew、nodebrewなど)を使用して入れている場合は、claude desktopがnpxを認識できないため、以下を参考にして設定ファイルを変更する。
https://zenn.dev/kozarusha/articles/1764c10816df79
https://zenn.dev/nekorush14/articles/d46635473779b0
https://qiita.com/kaisumi/items/aeb64ffa49235d33723a

**問題2: github tokenの作成**
githubにログインし、以下の通り選択。
Settings > Developer Settings > Personal access tokens (classic)
以下の項目にチェックを入れtokenを作成(おそらくgithub mcpを使うだけであれば以下の権限があれば十分かと思われる。)
・repo
tokenは1度しか表示されないため、控えておく。

## 4. 参考資料

- [CursorでPlaywright MCPを使う方法](https://qiita.com/NightOwl/items/f11fb2404a1858d8871a)

