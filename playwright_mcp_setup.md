# Playwright MCP セットアップガイド

## 前提条件

- Node.jsとnpmがインストールされていること

## 1. Cursor での設定

### 1.1 設定ファイルの作成

(Macの場合)メニューから Cursor > 基本設定 > Cursor Settings を選択し、
開いたCursor Settingsのウィンドウ左側にあるMCPの項目を選択します。
MCPの項目を選択後「+ Add new global MCP server」ボタンを押下し次の設定ファイルの内容を記載します。
MCP名の左側に表示されているドットが緑色になったら、設定成功。

### 3.2 設定ファイルの内容

```mcp.json
{
  "mcpServers": {
    "playwright-mcp": {
      "command": "npx",
      "args": [
        "@playwright/mcp@latest"
      ]
    }
  }
}
```

## 2. Claude Desktop での設定

### 2.1 PlaywrightとPlaywright MCPをインストール

以下のコマンドでPlaywrightとPlaywright MCPをインストールする。
```
npm add @playwright/mcp playwright
npm install
npx playwright --version

# 以下コマンドでブラウザとPlaywright Inspectorがひらけばインストール完了
npx playwright open
```

### 2.2 Claude DesktopにMCPを設定

(Macの場合)メニューから Claude > 設定... を選択し、設定画面が開いたら「開発者」タブを選択。
「設定の構成」ボタンを押下するとFinderが開くため、claude_desktop_config.jsonを探して編集する。
「開発者」タブにplaywrightが追加され、ステータスがrunningになっていれば設定成功。

### 2.3 設定ファイルの内容
playwrightの項目を追加。他のMCPを追加したい場合はfigma-developer-mcpのように記載する。

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

**問題2: Claude DesktopでNode.jsのエラーが発生する**
以下のようなエラーが発生する場合はNode.jsが古い可能性があります。
```
import packageJSON from '../package.json' with { type: 'json' };
                                          ^^^^

SyntaxError: Unexpected token 'with'
    at ModuleLoader.moduleStrategy (node:internal/modules/esm/translators:118:18)
    at callTranslator (node:internal/modules/esm/loader:265:14)
    at ModuleLoader.moduleProvider (node:internal/modules/esm/loader:270:30)
```

## 4. 参考資料

- [CursorでPlaywright MCPを使う方法](https://qiita.com/NightOwl/items/f11fb2404a1858d8871a)
- [Playwright MCPのGithub](https://github.com/microsoft/playwright-mcp)
- [Claude DesktopとPlaywright MCPを連携してみた](https://dev.classmethod.jp/articles/claude-desktop-playwright-mcp/)
