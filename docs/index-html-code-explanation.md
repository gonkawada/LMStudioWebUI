# index.html コード解説書

## 概要

このドキュメントは、LM Studio Chat WebUIの`index.html`ファイルの全コードを行単位で解説します。このファイルは単一のHTMLファイルとして、完全なWebアプリケーションを実装しています。

---

## ファイル構造

```
index.html
├── HTMLヘッダー（1-449行）
│   ├── メタタグとタイトル（1-6行）
│   ├── 外部リソースの読み込み（7-11行）
│   ├── CSSスタイル（12-382行）
│   └── JavaScriptライブラリの設定（383-449行）
├── HTMLボディ（450-490行）
│   ├── アプリケーション構造（451-488行）
│   └── コンテキストメニュー（489行）
└── JavaScriptコード（491-919行）
    ├── グローバル変数（492-512行）
    ├── 設定管理関数（514-535行）
    ├── ヘルパー関数（537-838行）
    └── イベントリスナー（840-919行）
```

---

## 詳細解説

### 1-11行: HTMLヘッダーと外部リソース

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no" />
  <title>LM Studio Chat</title>
```

**1行目**: HTML5のドキュメントタイプ宣言
**2行目**: HTML要素の開始タグ、言語を英語に設定
**3行目**: headセクションの開始
**4行目**: 文字エンコーディングをUTF-8に設定
**5行目**: ビューポート設定
  - `width=device-width`: デバイスの幅に合わせる
  - `initial-scale=1.0`: 初期ズームレベルを100%に設定
  - `maximum-scale=1.0`: 最大ズームレベルを100%に制限
  - `user-scalable=no`: ユーザーによるズームを無効化（モバイル対応）
**6行目**: ページタイトルを「LM Studio Chat」に設定

```html
  <!-- Google Font -->
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600&display=swap" rel="stylesheet" />
  <!-- Font Awesome Icons -->
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.7.2/css/all.min.css" integrity="sha512-Evv84Mr4kqVGRNSgIGL/F/aIDqQb7xQ2vcrdIwxfjThSH8CSR7PBEakCr51Ck+w+/U6swU2Im1vVX0SVk9ABhg==" crossorigin="anonymous" referrerpolicy="no-referrer" />
  <!-- Highlight.js CSS for code blocks -->
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/highlight.js/11.7.0/styles/atom-one-dark.min.css" />
```

**7行目**: コメント - Google Fontの読み込み
**8行目**: Google FontsからInterフォントを読み込み（ウェイト400, 500, 600）
**9行目**: コメント - Font Awesomeアイコンの読み込み
**10行目**: Font Awesome 6.7.2のCSSを読み込み（アイコン表示用）
**11行目**: コメント - Highlight.jsのCSSの読み込み
**12行目**: Highlight.jsのAtom One DarkテーマのCSSを読み込み（コードハイライト用）


### 13-382行: CSSスタイル定義

#### 13-28行: CSS変数とグローバルスタイル

```css
  <style>
    :root {
      /* Purple themed color scheme */
      --background-color: #1e0a3c;
      --text-color: #ffffff;
      --input-background: #2c0f4b;
      --user-message-color: #6c2d8c;
      --assistant-message-color: #442a6f;
      --button-color: #8e44ad;
      --accent-color: #bb86fc;
      --border-radius: 8px;
      --transition-speed: 0.3s;
      --shadow: 0 2px 8px rgba(0,0,0,0.3);
    }
```

**13行目**: styleタグの開始
**14行目**: `:root`セレクタ - CSS変数を定義するためのルート要素
**15行目**: コメント - 紫をテーマとしたカラースキーム
**16行目**: `--background-color`: 背景色（濃い紫 #1e0a3c）
**17行目**: `--text-color`: テキスト色（白 #ffffff）
**18行目**: `--input-background`: 入力フィールドの背景色（紫 #2c0f4b）
**19行目**: `--user-message-color`: ユーザーメッセージの背景色（明るい紫 #6c2d8c）
**20行目**: `--assistant-message-color`: アシスタントメッセージの背景色（中間の紫 #442a6f）
**21行目**: `--button-color`: ボタンの色（紫 #8e44ad）
**22行目**: `--accent-color`: アクセントカラー（明るい紫 #bb86fc）
**23行目**: `--border-radius`: 角の丸み（8px）
**24行目**: `--transition-speed`: トランジションの速度（0.3秒）
**25行目**: `--shadow`: 影のスタイル（黒の半透明）
**26行目**: `:root`セレクタの終了

```css
    * { box-sizing: border-box; }
    body, html {
      font-family: 'Inter', sans-serif;
      margin: 0;
      padding: 0;
      height: 100%;
      background-color: var(--background-color);
      color: var(--text-color);
    }
    #app { display: flex; flex-direction: column; height: 100%; }
```

**27行目**: すべての要素に`box-sizing: border-box`を適用（パディングとボーダーを幅に含める）
**28-35行目**: bodyとhtml要素のスタイル
  - **29行目**: Interフォントを使用、フォールバックはsans-serif
  - **30行目**: マージンを0に設定
  - **31行目**: パディングを0に設定
  - **32行目**: 高さを100%に設定（フルスクリーン）
  - **33行目**: 背景色をCSS変数から取得
  - **34行目**: テキスト色をCSS変数から取得
**36行目**: `#app`要素をFlexboxコンテナとして設定、縦方向に配置、高さ100%


#### 37-82行: ヘッダー部分のスタイル（サーバーURL、モデル選択、接続ボタン）

```css
    /* Header: Server URL & Model Selection */
    #server-url-container {
      padding: 0.75rem;
      background-color: var(--input-background);
      display: flex;
      flex-wrap: wrap;
      justify-content: space-between;
      align-items: center;
      box-shadow: var(--shadow);
    }
```

**37行目**: コメント - ヘッダー部分のスタイル
**38-46行目**: サーバーURLコンテナのスタイル
  - **39行目**: パディング0.75rem
  - **40行目**: 背景色を入力フィールド用の色に設定
  - **41行目**: Flexboxレイアウトを使用
  - **42行目**: 要素を折り返し可能に設定
  - **43行目**: 要素を両端揃えで配置
  - **44行目**: 要素を中央揃えで配置
  - **45行目**: 影を追加

```css
    #server-url {
      flex-grow: 1;
      padding: 0.5rem 0.75rem;
      margin-right: 0.5rem;
      margin-bottom: 0.5rem;
      border: none;
      border-radius: var(--border-radius);
      background-color: var(--background-color);
      color: var(--text-color);
      font-size: 0.95rem;
    }
```

**47-57行目**: サーバーURL入力フィールドのスタイル
  - **48行目**: 利用可能なスペースを埋めるように拡大
  - **49行目**: パディング（上下0.5rem、左右0.75rem）
  - **50行目**: 右マージン0.5rem
  - **51行目**: 下マージン0.5rem
  - **52行目**: ボーダーなし
  - **53行目**: 角の丸みをCSS変数から取得
  - **54行目**: 背景色を設定
  - **55行目**: テキスト色を設定
  - **56行目**: フォントサイズ0.95rem

```css
    /* Model selection dropdown */
    #model-select {
      padding: 0.5rem;
      font-size: 0.95rem;
      margin-right: 0.5rem;
      border: none;
      border-radius: var(--border-radius);
      background-color: var(--background-color);
      color: var(--text-color);
    }
```

**58行目**: コメント - モデル選択ドロップダウン
**59-67行目**: モデル選択ドロップダウンのスタイル
  - **60行目**: パディング0.5rem
  - **61行目**: フォントサイズ0.95rem
  - **62行目**: 右マージン0.5rem
  - **63行目**: ボーダーなし
  - **64行目**: 角の丸みを設定
  - **65行目**: 背景色を設定
  - **66行目**: テキスト色を設定

```css
    #connect-button {
      padding: 0.55rem 1rem;
      background-color: var(--button-color);
      color: var(--text-color);
      border: none;
      border-radius: var(--border-radius);
      cursor: pointer;
      font-size: 0.95rem;
      transition: background-color var(--transition-speed);
    }
    #connect-button:hover { background-color: var(--accent-color); }
```

**68-77行目**: 接続ボタンのスタイル
  - **69行目**: パディング（上下0.55rem、左右1rem）
  - **70行目**: 背景色をボタン用の色に設定
  - **71行目**: テキスト色を設定
  - **72行目**: ボーダーなし
  - **73行目**: 角の丸みを設定
  - **74行目**: カーソルをポインターに変更
  - **75行目**: フォントサイズ0.95rem
  - **76行目**: 背景色の変化にトランジション効果を適用
**78行目**: ホバー時に背景色をアクセントカラーに変更

```css
    #connection-status {
      width: 100%;
      text-align: center;
      padding: 0.5rem;
      font-size: 0.85rem;
      background-color: var(--input-background);
      color: #ddd;
    }
```

**79-86行目**: 接続ステータス表示のスタイル
  - **80行目**: 幅100%
  - **81行目**: テキストを中央揃え
  - **82行目**: パディング0.5rem
  - **83行目**: フォントサイズ0.85rem
  - **84行目**: 背景色を設定
  - **85行目**: テキスト色を薄いグレーに設定


#### 87-195行: サイドバーとチャット領域のスタイル

```css
    /* Main content: sidebar + chat area */
    #main-content {
      display: flex;
      flex-grow: 1;
      overflow: hidden;
    }
```

**87行目**: コメント - メインコンテンツ（サイドバー + チャット領域）
**88-92行目**: メインコンテンツコンテナのスタイル
  - **89行目**: Flexboxレイアウトを使用
  - **90行目**: 利用可能なスペースを埋めるように拡大
  - **91行目**: オーバーフローを隠す

```css
    /* Chat sidebar */
    #chat-sidebar {
      width: 250px;
      background-color: var(--input-background);
      border-right: 1px solid #333;
      overflow: hidden;
      transition: width var(--transition-speed) ease;
      position: relative;
      flex-shrink: 0;
    }
    #chat-sidebar.collapsed { width: 40px; }
```

**93行目**: コメント - チャットサイドバー
**94-103行目**: チャットサイドバーのスタイル
  - **95行目**: 幅250px
  - **96行目**: 背景色を設定
  - **97行目**: 右側に1pxの暗いボーダーを追加
  - **98行目**: オーバーフローを隠す
  - **99行目**: 幅の変化にトランジション効果を適用（イージング付き）
  - **100行目**: 相対位置指定
  - **101行目**: Flexboxで縮小しないように設定
**103行目**: サイドバーが折りたたまれた状態では幅を40pxに変更

```css
    /* Toggle sidebar button (right offset) */
    #toggle-sidebar {
      position: absolute;
      top: 10px;
      right: 10px;
      background-color: var(--button-color);
      border: none;
      border-radius: 50%;
      width: 30px;
      height: 30px;
      color: var(--text-color);
      cursor: pointer;
      transition: background-color var(--transition-speed);
      z-index: 10;
    }
    #toggle-sidebar:hover { background-color: var(--accent-color); }
```

**104行目**: コメント - サイドバートグルボタン（右寄せ）
**105-118行目**: トグルボタンのスタイル
  - **106行目**: 絶対位置指定
  - **107行目**: 上から10px
  - **108行目**: 右から10px
  - **109行目**: 背景色をボタン用の色に設定
  - **110行目**: ボーダーなし
  - **111行目**: 完全な円形（border-radius 50%）
  - **112行目**: 幅30px
  - **113行目**: 高さ30px
  - **114行目**: テキスト色を設定
  - **115行目**: カーソルをポインターに変更
  - **116行目**: 背景色の変化にトランジション効果を適用
  - **117行目**: z-indexを10に設定（他の要素より前面に表示）
**119行目**: ホバー時に背景色をアクセントカラーに変更

```css
    /* Sidebar content */
    .sidebar-content {
      padding: 1rem;
      transition: opacity var(--transition-speed) ease;
    }
    #chat-sidebar.collapsed .sidebar-content {
      opacity: 0;
      pointer-events: none;
    }
```

**120行目**: コメント - サイドバーコンテンツ
**121-124行目**: サイドバーコンテンツのスタイル
  - **122行目**: パディング1rem
  - **123行目**: 不透明度の変化にトランジション効果を適用
**125-128行目**: サイドバーが折りたたまれた状態のコンテンツスタイル
  - **126行目**: 不透明度を0に設定（非表示）
  - **127行目**: ポインターイベントを無効化（クリック不可）

```css
    #chat-sidebar-header {
      display: flex;
      justify-content: space-between;
      align-items: center;
      margin-bottom: 1rem;
      font-size: 1.1rem;
      font-weight: 600;
    }
```

**129-136行目**: サイドバーヘッダーのスタイル
  - **130行目**: Flexboxレイアウトを使用
  - **131行目**: 要素を両端揃えで配置
  - **132行目**: 要素を中央揃えで配置
  - **133行目**: 下マージン1rem
  - **134行目**: フォントサイズ1.1rem
  - **135行目**: フォントウェイト600（太字）

```css
    #new-chat-button {
      width: 100%;
      padding: 0.5rem;
      border: none;
      border-radius: var(--border-radius);
      background-color: var(--button-color);
      color: var(--text-color);
      cursor: pointer;
      margin-bottom: 1rem;
      transition: background-color var(--transition-speed);
      font-size: 0.9rem;
    }
    #new-chat-button:hover { background-color: var(--accent-color); }
```

**137-148行目**: 新規チャットボタンのスタイル
  - **138行目**: 幅100%
  - **139行目**: パディング0.5rem
  - **140行目**: ボーダーなし
  - **141行目**: 角の丸みを設定
  - **142行目**: 背景色をボタン用の色に設定
  - **143行目**: テキスト色を設定
  - **144行目**: カーソルをポインターに変更
  - **145行目**: 下マージン1rem
  - **146行目**: 背景色の変化にトランジション効果を適用
  - **147行目**: フォントサイズ0.9rem
**149行目**: ホバー時に背景色をアクセントカラーに変更

```css
    #chat-list { list-style: none; padding: 0; margin: 0; }
```

**150行目**: チャットリストのスタイル
  - リストマーカーなし、パディングとマージンを0に設定

```css
    /* Chat titles */
    #chat-list li {
      padding: 0.75rem;
      border-radius: var(--border-radius);
      margin-bottom: 0.5rem;
      background-color: var(--background-color);
      cursor: pointer;
      transition: background-color var(--transition-speed);
      white-space: nowrap;
      overflow: hidden;
      text-overflow: ellipsis;
      font-size: 0.9rem;
      position: relative;
    }
    #chat-list li:hover { background-color: var(--input-background); }
    #chat-list li.active { background-color: var(--button-color); font-weight: 600; }
```

**151行目**: コメント - チャットタイトル
**152-165行目**: チャットリスト項目のスタイル
  - **153行目**: パディング0.75rem
  - **154行目**: 角の丸みを設定
  - **155行目**: 下マージン0.5rem
  - **156行目**: 背景色を設定
  - **157行目**: カーソルをポインターに変更
  - **158行目**: 背景色の変化にトランジション効果を適用
  - **159行目**: テキストを折り返さない
  - **160行目**: オーバーフローを隠す
  - **161行目**: テキストが長い場合は省略記号（...）を表示
  - **162行目**: フォントサイズ0.9rem
  - **163行目**: 相対位置指定
**166行目**: ホバー時に背景色を変更
**167行目**: アクティブなチャットの背景色とフォントウェイトを変更

```css
    /* Custom context menu for deleting chats */
    #context-menu {
      display: none;
      position: absolute;
      background-color: var(--button-color);
      color: var(--text-color);
      padding: 0.5rem;
      border-radius: 4px;
      z-index: 1000;
      cursor: pointer;
      font-size: 0.9rem;
    }
```

**168行目**: コメント - チャット削除用のカスタムコンテキストメニュー
**169-180行目**: コンテキストメニューのスタイル
  - **170行目**: デフォルトで非表示
  - **171行目**: 絶対位置指定
  - **172行目**: 背景色をボタン用の色に設定
  - **173行目**: テキスト色を設定
  - **174行目**: パディング0.5rem
  - **175行目**: 角の丸み4px
  - **176行目**: z-indexを1000に設定（最前面に表示）
  - **177行目**: カーソルをポインターに変更
  - **178行目**: フォントサイズ0.9rem


#### 181-290行: チャットセクションとメッセージのスタイル

```css
    /* Chat section */
    #chat-section {
      flex-grow: 1;
      display: flex;
      flex-direction: column;
      overflow: hidden;
      background: linear-gradient(135deg, #2c0f4b, #1e0a3c);
      padding: 1rem;
      border-radius: 10px;
      margin: 1rem;
    }
```

**181行目**: コメント - チャットセクション
**182-191行目**: チャットセクションのスタイル
  - **183行目**: 利用可能なスペースを埋めるように拡大
  - **184行目**: Flexboxレイアウトを使用
  - **185行目**: 縦方向に配置
  - **186行目**: オーバーフローを隠す
  - **187行目**: グラデーション背景（135度、紫から濃い紫へ）
  - **188行目**: パディング1rem
  - **189行目**: 角の丸み10px
  - **190行目**: マージン1rem

```css
    #chat-container {
      flex-grow: 1;
      overflow-y: auto;
      padding: 1rem;
      display: flex;
      flex-direction: column;
      gap: 0.75rem;
      background: rgba(255,255,255,0.05);
      border-radius: 8px;
    }
```

**192-201行目**: チャットコンテナのスタイル
  - **193行目**: 利用可能なスペースを埋めるように拡大
  - **194行目**: 縦方向のスクロールを有効化
  - **195行目**: パディング1rem
  - **196行目**: Flexboxレイアウトを使用
  - **197行目**: 縦方向に配置
  - **198行目**: 要素間のギャップ0.75rem
  - **199行目**: 半透明の白い背景（5%の不透明度）
  - **200行目**: 角の丸み8px

```css
    /* Message card styling */
    .message {
      max-width: 85%;
      padding: 0.75rem 1rem;
      border-radius: var(--border-radius);
      word-wrap: break-word;
      font-size: 0.95rem;
      line-height: 1.5;
      background-color: var(--assistant-message-color);
      box-shadow: var(--shadow);
      animation: fadeIn 0.3s ease;
      position: relative;
    }
    .user-message { align-self: flex-end; background-color: var(--user-message-color); }
    .assistant-message { align-self: flex-start; background-color: var(--assistant-message-color); }
```

**202行目**: コメント - メッセージカードのスタイリング
**203-215行目**: メッセージの基本スタイル
  - **204行目**: 最大幅85%
  - **205行目**: パディング（上下0.75rem、左右1rem）
  - **206行目**: 角の丸みを設定
  - **207行目**: 長い単語を折り返す
  - **208行目**: フォントサイズ0.95rem
  - **209行目**: 行の高さ1.5
  - **210行目**: 背景色をアシスタントメッセージ用の色に設定
  - **211行目**: 影を追加
  - **212行目**: フェードインアニメーション（0.3秒、イージング付き）
  - **213行目**: 相対位置指定（コピーボタンを絶対位置で配置するため）
**215行目**: ユーザーメッセージは右寄せ、背景色を変更
**216行目**: アシスタントメッセージは左寄せ、背景色を変更

```css
    .message-header {
      font-weight: 600;
      margin-bottom: 0.35rem;
      font-size: 0.85rem;
    }
    .message-model {
      font-size: 0.75rem;
      color: #ccc;
      margin-bottom: 0.35rem;
    }
    .message-content { margin-bottom: 0.5rem; }
    .message-metrics { font-size: 0.75rem; color: #ccc; text-align: right; }
```

**216-227行目**: メッセージの各部分のスタイル
  - **216-220行目**: メッセージヘッダー（「You」または「Assistant」）
    - **217行目**: フォントウェイト600（太字）
    - **218行目**: 下マージン0.35rem
    - **219行目**: フォントサイズ0.85rem
  - **221-225行目**: モデル名表示
    - **222行目**: フォントサイズ0.75rem
    - **223行目**: テキスト色を薄いグレーに設定
    - **224行目**: 下マージン0.35rem
  - **226行目**: メッセージコンテンツの下マージン0.5rem
  - **227行目**: メトリクス表示（フォントサイズ0.75rem、薄いグレー、右寄せ）

```css
    /* Enhanced Markdown styling */
    .message-content h1,
    .message-content h2,
    .message-content h3,
    .message-content h4,
    .message-content h5,
    .message-content h6 {
      margin: 0.5rem 0;
      font-weight: 600;
    }
    .message-content p { margin: 0.5rem 0; line-height: 1.6; }
    .message-content code {
      background-color: rgba(27,31,35,0.15);
      padding: 0.2em 0.4em;
      border-radius: 4px;
      font-family: Consolas, Monaco, 'Andale Mono', 'Ubuntu Mono', monospace;
    }
    .message-content pre {
      background-color: #282c34;
      color: #abb2bf;
      padding: 0.8rem;
      overflow-x: auto;
      border-radius: 6px;
      margin: 0.5rem 0;
    }
    .message-content blockquote {
      border-left: 4px solid var(--button-color);
      margin: 1rem 0;
      padding: 0.5rem 1rem;
      color: #ccc;
      background: rgba(142,68,173,0.1);
    }
```

**228行目**: コメント - 拡張Markdownスタイリング
**229-237行目**: 見出し（h1-h6）のスタイル
  - **236行目**: マージン（上下0.5rem）
  - **237行目**: フォントウェイト600（太字）
**239行目**: 段落のスタイル（マージン0.5rem、行の高さ1.6）
**240-245行目**: インラインコードのスタイル
  - **241行目**: 半透明の暗い背景
  - **242行目**: パディング（上下0.2em、左右0.4em）
  - **243行目**: 角の丸み4px
  - **244行目**: モノスペースフォント
**246-252行目**: コードブロック（pre）のスタイル
  - **247行目**: 暗い背景色
  - **248行目**: テキスト色を薄いグレーに設定
  - **249行目**: パディング0.8rem
  - **250行目**: 横方向のスクロールを有効化
  - **251行目**: 角の丸み6px
  - **252行目**: マージン（上下0.5rem）
**253-259行目**: 引用ブロックのスタイル
  - **254行目**: 左側に4pxの紫色のボーダー
  - **255行目**: マージン（上下1rem）
  - **256行目**: パディング（上下0.5rem、左右1rem）
  - **257行目**: テキスト色を薄いグレーに設定
  - **258行目**: 半透明の紫色の背景


#### 260-382行: 入力コンテナ、レスポンシブデザイン、アニメーション

```css
    /* Input container */
    #input-container {
      padding: 0.75rem;
      background-color: rgba(44,15,75,0.8);
      border-top: 1px solid #333;
      display: flex;
      align-items: center;
      gap: 0.5rem;
      box-shadow: var(--shadow);
      border-radius: 10px;
    }
```

**260行目**: コメント - 入力コンテナ
**261-271行目**: 入力コンテナのスタイル
  - **262行目**: パディング0.75rem
  - **263行目**: 半透明の紫色の背景（80%の不透明度）
  - **264行目**: 上側に1pxの暗いボーダー
  - **265行目**: Flexboxレイアウトを使用
  - **266行目**: 要素を中央揃えで配置
  - **267行目**: 要素間のギャップ0.5rem
  - **268行目**: 影を追加
  - **269行目**: 角の丸み10px

```css
    /* Image upload button */
    #upload-button {
      background-color: var(--button-color);
      color: var(--text-color);
      border: none;
      border-radius: 50%;
      width: 2.75rem;
      height: 2.75rem;
      cursor: pointer;
      display: flex;
      justify-content: center;
      align-items: center;
      transition: background-color var(--transition-speed);
    }
    #upload-button:hover { background-color: var(--accent-color); }
```

**272行目**: コメント - 画像アップロードボタン
**273-285行目**: 画像アップロードボタンのスタイル
  - **274行目**: 背景色をボタン用の色に設定
  - **275行目**: テキスト色を設定
  - **276行目**: ボーダーなし
  - **277行目**: 完全な円形（border-radius 50%）
  - **278行目**: 幅2.75rem
  - **279行目**: 高さ2.75rem
  - **280行目**: カーソルをポインターに変更
  - **281行目**: Flexboxレイアウトを使用
  - **282行目**: 要素を中央揃えで配置（横方向）
  - **283行目**: 要素を中央揃えで配置（縦方向）
  - **284行目**: 背景色の変化にトランジション効果を適用
**286行目**: ホバー時に背景色をアクセントカラーに変更

```css
    /* Image preview */
    #image-preview { display: none; max-width: 50px; }
```

**287行目**: コメント - 画像プレビュー
**288行目**: 画像プレビューのスタイル（デフォルトで非表示、最大幅50px）

```css
    #user-input {
      flex-grow: 1;
      padding: 0.6rem 0.75rem;
      border: none;
      border-radius: var(--border-radius);
      background-color: var(--background-color);
      color: var(--text-color);
      font-size: 0.95rem;
    }
    #user-input::placeholder { color: #bbb; }
```

**289-298行目**: ユーザー入力フィールドのスタイル
  - **290行目**: 利用可能なスペースを埋めるように拡大
  - **291行目**: パディング（上下0.6rem、左右0.75rem）
  - **292行目**: ボーダーなし
  - **293行目**: 角の丸みを設定
  - **294行目**: 背景色を設定
  - **295行目**: テキスト色を設定
  - **296行目**: フォントサイズ0.95rem
**298行目**: プレースホルダーのテキスト色を薄いグレーに設定

```css
    #send-button {
      background-color: var(--button-color);
      color: var(--text-color);
      border: none;
      border-radius: 50%;
      width: 2.75rem;
      height: 2.75rem;
      cursor: pointer;
      display: flex;
      justify-content: center;
      align-items: center;
      transition: background-color var(--transition-speed);
    }
    #send-button:hover { background-color: var(--accent-color); }
```

**299-312行目**: 送信ボタンのスタイル
  - **300行目**: 背景色をボタン用の色に設定
  - **301行目**: テキスト色を設定
  - **302行目**: ボーダーなし
  - **303行目**: 完全な円形（border-radius 50%）
  - **304行目**: 幅2.75rem
  - **305行目**: 高さ2.75rem
  - **306行目**: カーソルをポインターに変更
  - **307行目**: Flexboxレイアウトを使用
  - **308行目**: 要素を中央揃えで配置（横方向）
  - **309行目**: 要素を中央揃えで配置（縦方向）
  - **310行目**: 背景色の変化にトランジション効果を適用
**312行目**: ホバー時に背景色をアクセントカラーに変更

```css
    @media (max-width: 480px) {
      .message { max-width: 90%; }
      #server-url-container { flex-direction: column; align-items: stretch; }
      #server-url, #connect-button, #model-select { width: 100%; margin-right: 0; margin-bottom: 0.5rem; }
      #chat-sidebar { display: none; }
    }
```

**313-318行目**: レスポンシブデザイン（画面幅480px以下の場合）
  - **314行目**: メッセージの最大幅を90%に変更
  - **315行目**: サーバーURLコンテナを縦方向に配置、要素を引き伸ばす
  - **316行目**: サーバーURL、接続ボタン、モデル選択を幅100%に変更、右マージンを0に、下マージンを0.5remに設定
  - **317行目**: チャットサイドバーを非表示

```css
    @keyframes fadeIn {
      from { opacity: 0; transform: translateY(10px); }
      to { opacity: 1; transform: translateY(0); }
    }
```

**319-322行目**: フェードインアニメーションの定義
  - **320行目**: 開始状態（不透明度0、下に10px移動）
  - **321行目**: 終了状態（不透明度1、元の位置）

```css
    /* Copy code button styles */
    .copy-btn {
      position: absolute;
      top: 5px;
      right: 5px;
      background-color: var(--accent-color);
      color: var(--text-color);
      border: none;
      border-radius: 4px;
      cursor: pointer;
      font-size: 0.75rem;
      padding: 2px 6px;
      opacity: 0.8;
      transition: opacity 0.2s;
    }
    .copy-btn:hover { opacity: 1; }
    /* Copy message button styles */
    .copy-message-btn {
      position: absolute;
      top: 5px;
      right: 5px;
      background-color: var(--accent-color);
      color: var(--text-color);
      border: none;
      border-radius: 4px;
      cursor: pointer;
      font-size: 0.7rem;
      padding: 3px 8px;
      opacity: 0;
      transition: opacity 0.2s;
      z-index: 5;
    }
    .message:hover .copy-message-btn { opacity: 0.8; }
    .copy-message-btn:hover { opacity: 1 !important; }
  </style>
```

**323行目**: コメント - コードコピーボタンのスタイル
**324-338行目**: コピーボタンのスタイル
  - **325行目**: 絶対位置指定
  - **326行目**: 上から5px
  - **327行目**: 右から5px
  - **328行目**: 背景色をアクセントカラーに設定
  - **329行目**: テキスト色を設定
  - **330行目**: ボーダーなし
  - **331行目**: 角の丸み4px
  - **332行目**: カーソルをポインターに変更
  - **333行目**: フォントサイズ0.75rem
  - **334行目**: パディング（上下2px、左右6px）
  - **335行目**: 不透明度0.8
  - **336行目**: 不透明度の変化にトランジション効果を適用（0.2秒）
**338行目**: ホバー時に不透明度を1に変更
**339行目**: コメント - メッセージコピーボタンのスタイル
**340-355行目**: メッセージコピーボタンのスタイル
  - **341行目**: 絶対位置指定
  - **342行目**: 上から5px
  - **343行目**: 右から5px
  - **344行目**: 背景色をアクセントカラーに設定
  - **345行目**: テキスト色を設定
  - **346行目**: ボーダーなし
  - **347行目**: 角の丸み4px
  - **348行目**: カーソルをポインターに変更
  - **349行目**: フォントサイズ0.7rem
  - **350行目**: パディング（上下3px、左右8px）
  - **351行目**: 不透明度0（デフォルトで非表示）
  - **352行目**: 不透明度の変化にトランジション効果を適用（0.2秒）
  - **353行目**: z-indexを5に設定（他の要素より前面に表示）
**355行目**: メッセージホバー時にコピーボタンの不透明度を0.8に変更
**356行目**: コピーボタンホバー時に不透明度を1に変更（!importantで優先）
**357行目**: styleタグの終了


### 340-449行: JavaScriptライブラリの設定

#### 340-361行: Marked.js（Markdownパーサー）の設定

```html
  <!-- Markdown rendering library -->
  <script src="https://cdn.jsdelivr.net/npm/marked/marked.min.js"></script>
  <script>
    marked.setOptions({
      gfm: true,
      breaks: true,
      smartypants: true,
      headerIds: false,
      highlight: function(code, lang) {
        if (lang && hljs.getLanguage(lang)) {
          return hljs.highlight(code, { language: lang }).value;
        }
        return code;
      }
    });
  </script>
```

**340行目**: コメント - Markdownレンダリングライブラリ
**341行目**: Marked.jsライブラリをCDNから読み込み
**342行目**: scriptタグの開始
**343行目**: Marked.jsの設定を開始
**344行目**: `gfm: true` - GitHub Flavored Markdown（GFM）を有効化
**345行目**: `breaks: true` - 改行を自動的に`<br>`タグに変換
**346行目**: `smartypants: true` - スマート引用符を有効化（"→"、'→'など）
**347行目**: `headerIds: false` - 見出しに自動的にIDを生成しない
**348-353行目**: `highlight`関数 - コードブロックのシンタックスハイライト
  - **349行目**: 言語が指定されており、Highlight.jsがその言語をサポートしている場合
  - **350行目**: Highlight.jsを使用してコードをハイライト
  - **352行目**: それ以外の場合は元のコードをそのまま返す
**354行目**: 設定の終了
**355行目**: scriptタグの終了

#### 362-364行: Highlight.js（シンタックスハイライト）の読み込み

```html
  <!-- Highlight.js -->
  <script src="https://cdnjs.cloudflare.com/ajax/libs/highlight.js/11.7.0/highlight.min.js"></script>
```

**362行目**: コメント - Highlight.js
**363行目**: Highlight.js 11.7.0をCDNから読み込み

#### 364-449行: MathJax（LaTeX数式レンダリング）の設定

```html
  <!-- MathJax configuration for LaTeX support -->
  <script>
    window.MathJax = {
      tex: {
        inlineMath: [['$', '$'], ['\\(', '\\)']],
        displayMath: [['$$', '$$'], ['\\[', '\\]']],
        processEscapes: true,
        packages: {'[+]': ['noerrors', 'noundefined']}
      },
      options: {
        skipHtmlTags: ['script', 'noscript', 'style', 'textarea', 'pre'],
        ignoreHtmlClass: 'tex2jax_ignore',
        processHtmlClass: 'tex2jax_process'
      },
      svg: { fontCache: 'global' }
    };
  </script>
  <script src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js" async></script>
</head>
```

**364行目**: コメント - LaTeXサポート用のMathJax設定
**365行目**: scriptタグの開始
**366行目**: MathJaxのグローバル設定オブジェクトを作成
**367-373行目**: `tex`設定 - LaTeX構文の設定
  - **368行目**: `inlineMath` - インライン数式のデリミタ（`$...$`と`\(...\)`）
  - **369行目**: `displayMath` - ディスプレイ数式のデリミタ（`$$...$$`と`\[...\]`）
  - **370行目**: `processEscapes: true` - エスケープシーケンスを処理
  - **371行目**: `packages` - エラー処理パッケージを追加（noerrors、noundefined）
**374-378行目**: `options`設定 - 処理オプション
  - **375行目**: `skipHtmlTags` - これらのHTMLタグ内のLaTeXを無視
  - **376行目**: `ignoreHtmlClass` - このクラスを持つ要素内のLaTeXを無視
  - **377行目**: `processHtmlClass` - このクラスを持つ要素内のLaTeXのみを処理
**379行目**: `svg`設定 - SVGレンダリングのフォントキャッシュをグローバルに設定
**380行目**: 設定オブジェクトの終了
**381行目**: scriptタグの終了
**382行目**: MathJax 3のライブラリをCDNから非同期で読み込み
**383行目**: headセクションの終了


### 384-490行: HTMLボディ（アプリケーション構造）

#### 384-395行: ヘッダー部分（サーバー接続とモデル選択）

```html
<body>
  <div id="app">
    <div id="server-url-container">
      <input type="text" id="server-url" placeholder="Enter LM Studio server address" />
      <select id="model-select" disabled>
        <option value="">Select a model</option>
      </select>
      <button id="connect-button"><i class="fas fa-plug"></i> Connect</button>
    </div>
    <div id="connection-status">Disconnected</div>
```

**384行目**: bodyタグの開始
**385行目**: アプリケーションのメインコンテナ（`id="app"`）
**386行目**: サーバーURLコンテナの開始
**387行目**: サーバーアドレス入力フィールド
  - `type="text"`: テキスト入力
  - `id="server-url"`: 要素ID
  - `placeholder`: プレースホルダーテキスト「Enter LM Studio server address」
**388-390行目**: モデル選択ドロップダウン
  - **388行目**: selectタグ、初期状態で無効化（`disabled`）
  - **389行目**: デフォルトオプション「Select a model」
**391行目**: 接続ボタン
  - Font Awesomeのプラグアイコン（`fa-plug`）と「Connect」テキスト
**392行目**: サーバーURLコンテナの終了
**393行目**: 接続ステータス表示（初期値「Disconnected」）

#### 394-417行: メインコンテンツ（サイドバーとチャット領域）

```html
    <div id="main-content">
      <!-- Chat Sidebar -->
      <div id="chat-sidebar">
        <button id="toggle-sidebar"><i class="fas fa-bars"></i></button>
        <div class="sidebar-content">
          <div id="chat-sidebar-header"><span>Chats</span></div>
          <button id="new-chat-button"><i class="fas fa-plus"></i> New Chat</button>
          <ul id="chat-list"></ul>
        </div>
      </div>
```

**394行目**: メインコンテンツコンテナの開始
**395行目**: コメント - チャットサイドバー
**396行目**: チャットサイドバーの開始
**397行目**: サイドバートグルボタン（Font Awesomeのバーアイコン `fa-bars`）
**398行目**: サイドバーコンテンツの開始
**399行目**: サイドバーヘッダー（「Chats」テキスト）
**400行目**: 新規チャットボタン（Font Awesomeのプラスアイコン `fa-plus` と「New Chat」テキスト）
**401行目**: チャットリスト（空のul要素、JavaScriptで動的に生成）
**402行目**: サイドバーコンテンツの終了
**403行目**: チャットサイドバーの終了

```html
      <!-- Chat Section -->
      <div id="chat-section">
        <div id="chat-container"></div>
        <div id="input-container">
          <button id="upload-button"><i class="fas fa-image"></i></button>
          <input type="file" id="image-upload" accept="image/*" style="display:none;" />
          <div id="image-preview"></div>
          <input type="text" id="user-input" placeholder="Type a message..." disabled />
          <button id="send-button"><i class="fas fa-paper-plane"></i></button>
        </div>
      </div>
    </div>
  </div>
```

**404行目**: コメント - チャットセクション
**405行目**: チャットセクションの開始
**406行目**: チャットコンテナ（メッセージが表示される領域、空のdiv要素）
**407行目**: 入力コンテナの開始
**408行目**: 画像アップロードボタン（Font Awesomeの画像アイコン `fa-image`）
**409行目**: 画像ファイル選択input（非表示、`accept="image/*"`で画像ファイルのみ受け付ける）
**410行目**: 画像プレビュー表示領域（空のdiv要素）
**411行目**: ユーザー入力フィールド
  - `type="text"`: テキスト入力
  - `id="user-input"`: 要素ID
  - `placeholder`: プレースホルダーテキスト「Type a message...」
  - `disabled`: 初期状態で無効化（接続後に有効化）
**412行目**: 送信ボタン（Font Awesomeの紙飛行機アイコン `fa-paper-plane`）
**413行目**: 入力コンテナの終了
**414行目**: チャットセクションの終了
**415行目**: メインコンテンツコンテナの終了
**416行目**: アプリケーションのメインコンテナの終了

```html
  <!-- Custom context menu for deleting chats -->
  <div id="context-menu">Delete Chat</div>
```

**417行目**: コメント - チャット削除用のカスタムコンテキストメニュー
**418行目**: コンテキストメニュー（「Delete Chat」テキスト、初期状態で非表示）


### 419-919行: JavaScriptコード

#### 419-512行: グローバル変数とDOM要素の取得

```javascript
  <script>
    // Global variables
    const chatContainer = document.getElementById('chat-container');
    const userInput = document.getElementById('user-input');
    const serverUrlInput = document.getElementById('server-url');
    const connectButton = document.getElementById('connect-button');
    const connectionStatus = document.getElementById('connection-status');
    const sendButton = document.getElementById('send-button');
    const newChatButton = document.getElementById('new-chat-button');
    const toggleSidebarButton = document.getElementById('toggle-sidebar');
    const chatSidebar = document.getElementById('chat-sidebar');
    const chatList = document.getElementById('chat-list');
    const contextMenu = document.getElementById('context-menu');
    const modelSelect = document.getElementById('model-select');
    const uploadButton = document.getElementById('upload-button');
    const imageUpload = document.getElementById('image-upload');
    const imagePreview = document.getElementById('image-preview');
```

**419行目**: scriptタグの開始
**420行目**: コメント - グローバル変数
**421-437行目**: DOM要素への参照を取得
  - **421行目**: `chatContainer` - チャットメッセージを表示するコンテナ
  - **422行目**: `userInput` - ユーザーメッセージ入力フィールド
  - **423行目**: `serverUrlInput` - サーバーアドレス入力フィールド
  - **424行目**: `connectButton` - 接続/切断ボタン
  - **425行目**: `connectionStatus` - 接続ステータス表示
  - **426行目**: `sendButton` - メッセージ送信ボタン
  - **427行目**: `newChatButton` - 新規チャット作成ボタン
  - **428行目**: `toggleSidebarButton` - サイドバートグルボタン
  - **429行目**: `chatSidebar` - チャットサイドバー
  - **430行目**: `chatList` - チャットリスト
  - **431行目**: `contextMenu` - コンテキストメニュー
  - **432行目**: `modelSelect` - モデル選択ドロップダウン
  - **433行目**: `uploadButton` - 画像アップロードボタン
  - **434行目**: `imageUpload` - 画像ファイル選択input
  - **435行目**: `imagePreview` - 画像プレビュー表示領域

```javascript
    let isConnected = false;
    let currentModel = '';
    let pendingImage = null;
    
    // Chat management: each chat has an id, name, and messages array.
    let chats = [];
    let currentChat = null;
```

**438-444行目**: アプリケーションの状態を管理する変数
  - **438行目**: `isConnected` - サーバーへの接続状態（初期値: false）
  - **439行目**: `currentModel` - 現在選択されているモデルID（初期値: 空文字列）
  - **440行目**: `pendingImage` - アップロードされた画像データ（初期値: null）
  - **442行目**: コメント - チャット管理の説明
  - **443行目**: `chats` - すべてのチャットセッションを格納する配列（初期値: 空配列）
  - **444行目**: `currentChat` - 現在アクティブなチャットセッション（初期値: null）


#### 446-467行: loadSettings関数（保存された設定を読み込む）

```javascript
    // Load saved settings from localStorage
    function loadSettings() {
      const savedServerUrl = localStorage.getItem('lmstudio_server_url');
      const savedModel = localStorage.getItem('lmstudio_model');
      
      if (savedServerUrl) {
        serverUrlInput.value = savedServerUrl;
      }
      
      if (savedModel) {
        currentModel = savedModel;
      }
    }
```

**446行目**: コメント - localStorageから保存された設定を読み込む
**447行目**: `loadSettings`関数の定義
**448行目**: localStorageから保存されたサーバーURLを取得（キー: 'lmstudio_server_url'）
**449行目**: localStorageから保存されたモデルIDを取得（キー: 'lmstudio_model'）
**451-453行目**: 保存されたサーバーURLが存在する場合の処理
  - **452行目**: サーバーURL入力フィールドに保存された値を設定
**455-457行目**: 保存されたモデルIDが存在する場合の処理
  - **456行目**: `currentModel`変数に保存された値を設定
**458行目**: 関数の終了


#### 460-472行: saveSettings関数（設定をlocalStorageに保存）

```javascript
    // Save settings to localStorage
    function saveSettings() {
      const serverUrl = serverUrlInput.value.trim();
      if (serverUrl) {
        localStorage.setItem('lmstudio_server_url', serverUrl);
      }
      
      if (currentModel) {
        localStorage.setItem('lmstudio_model', currentModel);
      }
    }
```

**460行目**: コメント - localStorageに設定を保存
**461行目**: `saveSettings`関数の定義
**462行目**: サーバーURL入力フィールドの値を取得し、前後の空白を削除
**463-465行目**: サーバーURLが空でない場合の処理
  - **464行目**: localStorageにサーバーURLを保存（キー: 'lmstudio_server_url'）
**467-469行目**: 現在のモデルIDが存在する場合の処理
  - **468行目**: localStorageにモデルIDを保存（キー: 'lmstudio_model'）
**470行目**: 関数の終了


#### 474-497行: attachCopyButtons関数（コードブロックにコピーボタンを追加）

#### 474-497行: attachCopyButtons関数（コードブロックにコピーボタンを追加）

```javascript
    // Helper: Attach copy code buttons to code blocks
    function attachCopyButtons(container) {
      container.querySelectorAll('pre').forEach(pre => {
        if (!pre.querySelector('.copy-btn')) {
          pre.style.position = 'relative';
          const button = document.createElement('button');
          button.className = 'copy-btn';
          button.innerHTML = '<i class="fas fa-copy"></i> Copy';
          button.addEventListener('click', () => {
            const codeText = pre.querySelector('code').innerText;
            navigator.clipboard.writeText(codeText)
              .then(() => {
                button.innerText = "Copied!";
                setTimeout(() => {
                  button.innerHTML = '<i class="fas fa-copy"></i> Copy';
                }, 2000);
              })
              .catch(err => {
                console.error('Failed to copy code: ', err);
              });
          });
          pre.appendChild(button);
        }
      });
    }
```

**474行目**: コメント - コードブロックにコピーボタンを追加するヘルパー関数
**475行目**: `attachCopyButtons`関数の定義（引数: container - DOM要素）
**476行目**: コンテナ内のすべての`<pre>`要素を取得し、各要素に対して処理を実行
**477行目**: コピーボタンがまだ存在しない場合のみ処理を実行
**478行目**: `<pre>`要素の位置を相対位置に設定（ボタンを絶対位置で配置するため）
**479行目**: ボタン要素を作成
**480行目**: ボタンのクラス名を`copy-btn`に設定
**481行目**: ボタンの内容をFont Awesomeのコピーアイコンと「Copy」テキストに設定
**482行目**: ボタンのクリックイベントリスナーを追加
**483行目**: `<code>`要素内のテキストを取得
**484行目**: Clipboard APIを使用してテキストをクリップボードにコピー
**485-489行目**: コピー成功時の処理
  - **486行目**: ボタンのテキストを「Copied!」に変更
  - **487-489行目**: 2秒後にボタンのテキストを元に戻す
**490-492行目**: コピー失敗時のエラーハンドリング
  - **491行目**: エラーをコンソールに出力
**494行目**: ボタンを`<pre>`要素に追加
**497行目**: 関数の終了
  - **458-460行目**: 2秒後にボタンのテキストを元に戻す
**461-463行目**: コピー失敗時の処理
  - **462行目**: エラーをコンソールに出力
**465行目**: ボタンを`<pre>`要素に追加
**466行目**: if文の終了
**467行目**: forEachループの終了
**468行目**: 関数の終了

#### 469-516行: addMessage関数（メッセージをDOMに追加）

```javascript
    // Adds a message to the DOM and (optionally) stores it.
    // If store is false, the message is only displayed and not added to currentChat.messages.
    function addMessage(content, isUser, metrics = null, store = true) {
      const messageDiv = document.createElement('div');
      messageDiv.classList.add('message', isUser ? 'user-message' : 'assistant-message');
    
      const headerDiv = document.createElement('div');
      headerDiv.classList.add('message-header');
      headerDiv.textContent = isUser ? 'You' : 'Assistant';
      messageDiv.appendChild(headerDiv);
```

**469-471行目**: コメント - メッセージをDOMに追加し、オプションで保存する関数の説明
**472行目**: `addMessage`関数の定義
  - 引数: `content`（メッセージ内容）、`isUser`（ユーザーメッセージかどうか）、`metrics`（パフォーマンスメトリクス、デフォルト: null）、`store`（チャット履歴に保存するかどうか、デフォルト: true）
**473行目**: メッセージ用のdiv要素を作成
**474行目**: メッセージのクラスを追加（`message`と、ユーザーメッセージなら`user-message`、そうでなければ`assistant-message`）
**476-479行目**: メッセージヘッダーの作成
  - **476行目**: ヘッダー用のdiv要素を作成
  - **477行目**: ヘッダーのクラスを`message-header`に設定
  - **478行目**: ヘッダーのテキストを設定（ユーザーメッセージなら「You」、そうでなければ「Assistant」）
  - **479行目**: ヘッダーをメッセージdivに追加

```javascript
      if (!isUser && currentModel) {
        const modelDiv = document.createElement('div');
        modelDiv.classList.add('message-model');
        modelDiv.textContent = currentModel;
        messageDiv.appendChild(modelDiv);
      }
```

**481-486行目**: アシスタントメッセージで、かつモデルが選択されている場合、モデル名を表示
  - **482行目**: モデル名表示用のdiv要素を作成
  - **483行目**: モデル名のクラスを`message-model`に設定
  - **484行目**: モデル名のテキストを設定
  - **485行目**: モデル名をメッセージdivに追加

```javascript
      const contentDiv = document.createElement('div');
      contentDiv.classList.add('message-content');
      contentDiv.innerHTML = marked.parse(content);
      messageDiv.appendChild(contentDiv);
```

**488-491行目**: メッセージコンテンツの作成
  - **488行目**: コンテンツ用のdiv要素を作成
  - **489行目**: コンテンツのクラスを`message-content`に設定
  - **490行目**: Marked.jsを使用してMarkdownをHTMLに変換し、コンテンツに設定
  - **491行目**: コンテンツをメッセージdivに追加

```javascript
      if (metrics) {
        const metricsDiv = document.createElement('div');
        metricsDiv.classList.add('message-metrics');
        metricsDiv.textContent = metrics;
        messageDiv.appendChild(metricsDiv);
      }
```

**493-498行目**: メトリクスが存在する場合、メトリクスを表示
  - **494行目**: メトリクス表示用のdiv要素を作成
  - **495行目**: メトリクスのクラスを`message-metrics`に設定
  - **496行目**: メトリクスのテキストを設定
  - **497行目**: メトリクスをメッセージdivに追加

```javascript
      // Add copy message button
      const copyMessageBtn = document.createElement('button');
      copyMessageBtn.className = 'copy-message-btn';
      copyMessageBtn.innerHTML = '<i class="fas fa-copy"></i>';
      copyMessageBtn.title = 'Copy message';
      copyMessageBtn.addEventListener('click', () => {
        // Get the text content without HTML tags
        const textContent = contentDiv.innerText;
        navigator.clipboard.writeText(textContent)
          .then(() => {
            copyMessageBtn.innerHTML = '<i class="fas fa-check"></i>';
            setTimeout(() => {
              copyMessageBtn.innerHTML = '<i class="fas fa-copy"></i>';
            }, 2000);
          })
          .catch(err => {
            console.error('Failed to copy message: ', err);
          });
      });
      messageDiv.appendChild(copyMessageBtn);
    
      chatContainer.appendChild(messageDiv);
      chatContainer.scrollTop = chatContainer.scrollHeight;
```

**499行目**: コメント - メッセージコピーボタンを追加
**500-519行目**: メッセージコピーボタンの作成と機能実装
  - **500行目**: ボタン要素を作成
  - **501行目**: ボタンのクラス名を`copy-message-btn`に設定
  - **502行目**: ボタンの内容をFont Awesomeのコピーアイコンに設定
  - **503行目**: ボタンのツールチップを「Copy message」に設定
  - **504行目**: ボタンのクリックイベントリスナーを追加
  - **505行目**: コメント - HTMLタグを除いたテキストコンテンツを取得
  - **506行目**: コンテンツdivのテキストコンテンツを取得
  - **507行目**: Clipboard APIを使用してテキストをクリップボードにコピー
  - **508-513行目**: コピー成功時の処理
    - **509行目**: ボタンのアイコンをチェックマークに変更
    - **510-512行目**: 2秒後にボタンのアイコンを元に戻す
  - **514-516行目**: コピー失敗時の処理
    - **515行目**: エラーをコンソールに出力
  - **517行目**: イベントリスナーの終了
  - **518行目**: ボタンをメッセージdivに追加

**520-521行目**: メッセージをチャットコンテナに追加し、スクロール
  - **520行目**: メッセージdivをチャットコンテナに追加
  - **521行目**: チャットコンテナを最下部までスクロール

```javascript
      messageDiv.querySelectorAll('pre code').forEach(block => {
        hljs.highlightElement(block);
      });
```

**523-525行目**: メッセージ内のすべてのコードブロックにHighlight.jsを適用

```javascript
      if (typeof MathJax !== 'undefined') {
        MathJax.typesetPromise([messageDiv]).catch(err => console.error('MathJax typeset failed:', err));
      }
      
      attachCopyButtons(messageDiv);
      
      if (store && currentChat) {
        currentChat.messages.push({ content, isUser, metrics, isImage: false });
      }
    }
```

**507-516行目**: MathJaxレンダリング、コピーボタン追加、チャット履歴への保存
  - **507-509行目**: MathJaxが利用可能な場合、数式をレンダリング
  - **511行目**: コピーボタンを追加
  - **513-515行目**: `store`がtrueで、かつ現在のチャットが存在する場合、メッセージをチャット履歴に保存
**516行目**: 関数の終了


#### 517-527行: addImageMessage関数（画像メッセージを追加）

```javascript
    // Adds an image message.
    function addImageMessage(dataURL, promptText) {
      // Display image message in the UI
      addMessage(`<img src="${dataURL}" style="max-width:100%; border-radius: var(--border-radius);" />`, true);
      // Mark the last message in the history as an image message
      const lastMsg = currentChat.messages[currentChat.messages.length - 1];
      lastMsg.isImage = true;
      lastMsg.imageData = dataURL;
      lastMsg.text = promptText;
    }
```

**517行目**: コメント - 画像メッセージを追加する関数
**518行目**: `addImageMessage`関数の定義（引数: `dataURL` - Base64画像データ、`promptText` - 画像に付随するテキスト）
**519行目**: コメント - UIに画像メッセージを表示
**520行目**: `addMessage`関数を使用して画像を表示（imgタグを含むHTML文字列、ユーザーメッセージとして）
**521行目**: コメント - 履歴の最後のメッセージを画像メッセージとしてマーク
**522行目**: チャット履歴の最後のメッセージを取得
**523行目**: `isImage`フラグをtrueに設定
**524行目**: 画像データを保存
**525行目**: テキストプロンプトを保存
**526行目**: 関数の終了

#### 527-535行: createNewChat関数（新しいチャットを作成）

```javascript
    // Creates a new chat.
    function createNewChat() {
      const chatId = Date.now();
      const newChat = { id: chatId, name: `Conversation ${chats.length + 1}`, messages: [] };
      chats.push(newChat);
      currentChat = newChat;
      updateChatList();
      chatContainer.innerHTML = '';
    }
```

**527行目**: コメント - 新しいチャットを作成する関数
**528行目**: `createNewChat`関数の定義
**529行目**: 現在のタイムスタンプをチャットIDとして使用
**530行目**: 新しいチャットオブジェクトを作成
  - `id`: チャットID
  - `name`: デフォルト名「Conversation N」（Nはチャット番号）
  - `messages`: 空のメッセージ配列
**531行目**: 新しいチャットをchats配列に追加
**532行目**: 新しいチャットを現在のチャットとして設定
**533行目**: チャットリストを更新
**534行目**: チャットコンテナをクリア
**535行目**: 関数の終了

#### 536-554行: updateChatList関数（チャットリストを更新）

```javascript
    // Renders the chat list in the sidebar.
    function updateChatList() {
      chatList.innerHTML = '';
      chats.forEach(chat => {
        const li = document.createElement('li');
        li.textContent = chat.name;
        li.dataset.chatId = chat.id;
        if (currentChat && chat.id === currentChat.id) li.classList.add('active');
        li.addEventListener('click', () => {
          if (currentChat && chat.id === currentChat.id) return;
          currentChat = chat;
          loadChat(chat);
          updateChatList();
        });
        li.addEventListener('contextmenu', (e) => {
          e.preventDefault();
          showContextMenu(e.pageX, e.pageY, chat.id);
        });
        chatList.appendChild(li);
      });
    }
```

**536行目**: コメント - サイドバーにチャットリストをレンダリングする関数
**537行目**: `updateChatList`関数の定義
**538行目**: チャットリストをクリア
**539行目**: すべてのチャットに対して処理を実行
**540行目**: リスト項目（li）要素を作成
**541行目**: リスト項目のテキストをチャット名に設定
**542行目**: リスト項目のdata属性にチャットIDを設定
**543行目**: 現在のチャットの場合、`active`クラスを追加
**544-549行目**: クリックイベントリスナーを追加
  - **545行目**: すでに現在のチャットの場合は何もしない
  - **546行目**: クリックされたチャットを現在のチャットとして設定
  - **547行目**: チャットを読み込む
  - **548行目**: チャットリストを更新（アクティブ状態を反映）
**550-553行目**: 右クリック（コンテキストメニュー）イベントリスナーを追加
  - **551行目**: デフォルトのコンテキストメニューを無効化
  - **552行目**: カスタムコンテキストメニューを表示
**554行目**: リスト項目をチャットリストに追加
**555行目**: forEachループの終了
**556行目**: 関数の終了

#### 557-565行: loadChat関数（チャットを読み込む）

```javascript
    // Loads a chat's messages into the chat container.
    function loadChat(chat) {
      chatContainer.innerHTML = '';
      chat.messages.forEach(message => {
        // When loading, we display messages without storing them again.
        addMessage(message.content, message.isUser, message.metrics, false);
      });
    }
```

**557行目**: コメント - チャットのメッセージをチャットコンテナに読み込む関数
**558行目**: `loadChat`関数の定義（引数: `chat` - チャットオブジェクト）
**559行目**: チャットコンテナをクリア
**560行目**: チャットのすべてのメッセージに対して処理を実行
**561行目**: コメント - 読み込み時は、メッセージを再度保存しない
**562行目**: `addMessage`関数を使用してメッセージを表示（`store`パラメータをfalseに設定）
**563行目**: forEachループの終了
**564行目**: 関数の終了

#### 565-577行: コンテキストメニュー関連の関数

```javascript
    // Custom context menu for deleting chats.
    function showContextMenu(x, y, chatId) {
      contextMenu.style.left = x + "px";
      contextMenu.style.top = y + "px";
      contextMenu.style.display = "block";
      contextMenu.onclick = () => {
        deleteChat(chatId);
        hideContextMenu();
      };
    }
    function hideContextMenu() { contextMenu.style.display = "none"; }
    document.addEventListener('click', () => { if (contextMenu.style.display === "block") hideContextMenu(); });
```

**565行目**: コメント - チャット削除用のカスタムコンテキストメニュー
**566行目**: `showContextMenu`関数の定義（引数: `x`, `y` - 表示位置、`chatId` - チャットID）
**567行目**: コンテキストメニューの左位置を設定
**568行目**: コンテキストメニューの上位置を設定
**569行目**: コンテキストメニューを表示
**570-573行目**: コンテキストメニューのクリックイベントリスナーを設定
  - **571行目**: チャットを削除
  - **572行目**: コンテキストメニューを非表示
**574行目**: 関数の終了
**575行目**: `hideContextMenu`関数の定義（コンテキストメニューを非表示）
**576行目**: ドキュメント全体のクリックイベントリスナーを追加（コンテキストメニューが表示されている場合は非表示にする）

#### 577-589行: deleteChat関数（チャットを削除）

```javascript
    function deleteChat(chatId) {
      chats = chats.filter(c => c.id != chatId);
      if (currentChat && currentChat.id == chatId) {
        currentChat = chats.length > 0 ? chats[0] : null;
        if (!currentChat) createNewChat();
        else loadChat(currentChat);
      }
      updateChatList();
    }
```

**577行目**: `deleteChat`関数の定義（引数: `chatId` - 削除するチャットのID）
**578行目**: 指定されたIDのチャットをchats配列から削除
**579行目**: 削除されたチャットが現在のチャットの場合
**580行目**: 別のチャットを現在のチャットとして設定（チャットが残っている場合は最初のチャット、そうでなければnull）
**581行目**: チャットが残っていない場合、新しいチャットを作成
**582行目**: チャットが残っている場合、そのチャットを読み込む
**583行目**: if文の終了
**584行目**: チャットリストを更新
**585行目**: 関数の終了


#### 586-598行: ejectCurrentModel関数（モデルをアンロード）

```javascript
    // Ejects the currently loaded model.
    async function ejectCurrentModel(oldModel) {
      const serverUrl = serverUrlInput.value.trim();
      try {
        await fetch(`${serverUrl}/v1/model/eject`, {
          method: 'POST',
          headers: { 'Content-Type': 'application/json' },
          body: JSON.stringify({ model: oldModel })
        });
        console.log(`Model ${oldModel} ejected.`);
      } catch (error) {
        console.error("Error ejecting model:", error);
      }
    }
```

**586行目**: コメント - 現在ロードされているモデルをアンロードする関数
**587行目**: `ejectCurrentModel`関数の定義（非同期関数、引数: `oldModel` - アンロードするモデルID）
**588行目**: サーバーURLを取得（前後の空白を削除）
**589行目**: try-catchブロックの開始
**590-594行目**: POST /v1/model/eject エンドポイントを呼び出し
  - **590行目**: fetchを使用してリクエストを送信（await）
  - **591行目**: HTTPメソッドをPOSTに設定
  - **592行目**: Content-Typeヘッダーをapplication/jsonに設定
  - **593行目**: リクエストボディにモデルIDを含むJSONを設定
**595行目**: コンソールにモデルがアンロードされたことを出力
**596-598行目**: エラーハンドリング
  - **597行目**: エラーをコンソールに出力
**599行目**: 関数の終了

#### 600-619行: buildConversationHistory関数（会話履歴を構築）

```javascript
    // Build conversation history without merging messages.
    // The history starts with the system prompt, then includes each stored message in order.
    function buildConversationHistory() {
      const systemPrompt = currentChat.messages.some(msg => msg.isImage)
        ? "You are an AI assistant that analyzes images."
        : "You are an intelligent assistant. You always provide well-reasoned answers that are both correct and helpful.";
      const history = [{ role: 'system', content: systemPrompt }];
      currentChat.messages.forEach(msg => {
        if (msg.isImage) {
          history.push({
            role: msg.isUser ? 'user' : 'assistant',
            content: [
              { type: "text", text: msg.text || "What's in this image?" },
              { type: "image_url", image_url: { url: msg.imageData } }
            ]
          });
        } else {
          history.push({ role: msg.isUser ? 'user' : 'assistant', content: msg.content });
        }
      });
      return history;
    }
```

**600-601行目**: コメント - メッセージをマージせずに会話履歴を構築する関数の説明
**602行目**: `buildConversationHistory`関数の定義
**603-605行目**: システムプロンプトを決定
  - **603行目**: チャットに画像メッセージが含まれているかチェック
  - **604行目**: 画像メッセージがある場合: 「You are an AI assistant that analyzes images.」
  - **605行目**: 画像メッセージがない場合: 「You are an intelligent assistant. You always provide well-reasoned answers that are both correct and helpful.」
**606行目**: システムプロンプトを含む履歴配列を初期化
**607行目**: チャットのすべてのメッセージに対して処理を実行
**608-617行目**: メッセージを履歴に追加
  - **608行目**: 画像メッセージの場合
  - **609-616行目**: 画像メッセージを履歴に追加
    - **610行目**: ロールを設定（ユーザーまたはアシスタント）
    - **611-615行目**: コンテンツ配列を設定
      - **612行目**: テキストオブジェクト（テキストがない場合はデフォルトプロンプト）
      - **613行目**: 画像URLオブジェクト
  - **617行目**: テキストメッセージの場合
  - **618行目**: テキストメッセージを履歴に追加
**619行目**: forEachループの終了
**620行目**: 履歴を返す
**621行目**: 関数の終了

#### 622-631行: モデル選択変更イベントリスナー

```javascript
    // Model selection change: eject old model if needed.
    modelSelect.addEventListener('change', async (e) => {
      const newModel = e.target.value;
      if (currentModel && currentModel !== newModel) {
        modelSelect.disabled = true;
        await ejectCurrentModel(currentModel);
      }
      currentModel = newModel;
      modelSelect.disabled = false;
    });
```

**622行目**: コメント - モデル選択変更時の処理
**623行目**: モデル選択ドロップダウンの変更イベントリスナーを追加（非同期関数）
**624行目**: 新しいモデルIDを取得
**625行目**: 現在のモデルが存在し、かつ新しいモデルと異なる場合
**626行目**: モデル選択ドロップダウンを一時的に無効化
**627行目**: 現在のモデルをアンロード（await）
**628行目**: if文の終了
**629行目**: 現在のモデルを新しいモデルに更新
**630行目**: モデル選択ドロップダウンを有効化
**631行目**: イベントリスナーの終了


#### 632-668行: connectToServer関数（サーバーに接続）

```javascript
    // Connect to server and populate model dropdown.
    async function connectToServer() {
      const serverUrl = serverUrlInput.value.trim();
      if (!serverUrl) {
        updateConnectionStatus('Please enter a valid server address', false);
        return;
      }
      try {
        updateConnectionStatus('Connecting...', false);
        const response = await fetch(`${serverUrl}/v1/models`, {
          method: 'GET',
          headers: { 'Content-Type': 'application/json' }
        });
        if (!response.ok) throw new Error('Server response was not ok');
        const data = await response.json();
        if (data && data.data && data.data.length > 0) {
          modelSelect.innerHTML = "";
          data.data.forEach(model => {
            const option = document.createElement('option');
            option.value = model.id;
            option.textContent = model.id;
            modelSelect.appendChild(option);
          });
          modelSelect.disabled = false;
          currentModel = modelSelect.value;
          isConnected = true;
          updateConnectionStatus('Connected', true);
          userInput.disabled = false;
          sendButton.disabled = false;
          if (!currentChat) createNewChat();
          // Display connection message without storing it in the chat history
          addMessage('Connected to LM Studio server. You can start chatting now!', false, null, false);
        } else {
          throw new Error('No models available');
        }
      } catch (error) {
        console.error('Error:', error);
        updateConnectionStatus('Failed to connect', false);
        addMessage('Error: Unable to connect to the LM Studio server. Please check the address and try again.', false);
      }
    }
```

**632行目**: コメント - サーバーに接続し、モデルドロップダウンを更新する関数
**633行目**: `connectToServer`関数の定義（非同期関数）
**634行目**: サーバーURLを取得（前後の空白を削除）
**635-638行目**: サーバーURLが空の場合の処理
  - **636行目**: 接続ステータスを更新（「Please enter a valid server address」）
  - **637行目**: 関数を終了
**639行目**: try-catchブロックの開始
**640行目**: 接続ステータスを「Connecting...」に更新
**641-644行目**: GET /v1/models エンドポイントを呼び出し
  - **641行目**: fetchを使用してリクエストを送信（await）
  - **642行目**: HTTPメソッドをGETに設定
  - **643行目**: Content-Typeヘッダーをapplication/jsonに設定
**645行目**: レスポンスが正常でない場合、エラーをスロー
**646行目**: レスポンスをJSONとしてパース
**647行目**: データが存在し、かつモデルが1つ以上ある場合
**648行目**: モデル選択ドロップダウンをクリア
**649-654行目**: すべてのモデルをドロップダウンに追加
  - **650行目**: option要素を作成
  - **651行目**: optionの値をモデルIDに設定
  - **652行目**: optionのテキストをモデルIDに設定
  - **653行目**: optionをドロップダウンに追加
**655行目**: モデル選択ドロップダウンを有効化
**656行目**: 現在のモデルをドロップダウンの値に設定
**657行目**: 接続状態をtrueに設定
**658行目**: 接続ステータスを「Connected」に更新
**659行目**: ユーザー入力フィールドを有効化
**660行目**: 送信ボタンを有効化
**661行目**: 現在のチャットが存在しない場合、新しいチャットを作成
**662行目**: コメント - 接続メッセージを表示（チャット履歴には保存しない）
**663行目**: 接続メッセージを表示（`store`パラメータをfalseに設定）
**664-666行目**: モデルが利用できない場合の処理
  - **665行目**: エラーをスロー
**667-671行目**: エラーハンドリング
  - **668行目**: エラーをコンソールに出力
  - **669行目**: 接続ステータスを「Failed to connect」に更新
  - **670行目**: エラーメッセージを表示
**672行目**: 関数の終了

#### 673-682行: updateConnectionStatus関数（接続ステータスを更新）

```javascript
    function updateConnectionStatus(message, connected) {
      connectionStatus.textContent = message;
      connectionStatus.style.color = connected ? 'var(--accent-color)' : '#f44336';
      connectButton.textContent = connected ? 'Disconnect' : 'Connect';
      serverUrlInput.disabled = connected;
      userInput.disabled = !connected;
      sendButton.disabled = !connected;
    }
```

**673行目**: `updateConnectionStatus`関数の定義（引数: `message` - ステータスメッセージ、`connected` - 接続状態）
**674行目**: 接続ステータスのテキストを更新
**675行目**: 接続ステータスの色を更新（接続時: アクセントカラー、切断時: 赤）
**676行目**: 接続ボタンのテキストを更新（接続時: 「Disconnect」、切断時: 「Connect」）
**677行目**: サーバーURL入力フィールドの有効/無効を切り替え（接続時: 無効、切断時: 有効）
**678行目**: ユーザー入力フィールドの有効/無効を切り替え（接続時: 有効、切断時: 無効）
**679行目**: 送信ボタンの有効/無効を切り替え（接続時: 有効、切断時: 無効）
**680行目**: 関数の終了


#### 681-768行: sendMessage関数（メッセージを送信）

この関数は長いため、複数のセクションに分けて解説します。

##### セクション1: メッセージの準備と送信（681-710行）

```javascript
    // Send message: if a pending image exists, include it.
    async function sendMessage() {
      let message = userInput.value.trim();
      if (!message && !pendingImage) return;
    
      if (pendingImage) {
        let promptText = message || "What's in this image?";
        addImageMessage(pendingImage, promptText);
        pendingImage = null;
        imagePreview.style.display = "none";
        userInput.value = "";
      } else {
        addMessage(message, true);
      }
    
      const conversationHistory = buildConversationHistory();
```

**681行目**: コメント - メッセージを送信する関数（画像がある場合は含める）
**682行目**: `sendMessage`関数の定義（非同期関数）
**683行目**: ユーザー入力を取得（前後の空白を削除）
**684行目**: メッセージと画像の両方が空の場合、関数を終了
**686-693行目**: 画像がある場合の処理
  - **687行目**: プロンプトテキストを設定（メッセージがない場合はデフォルトプロンプト）
  - **688行目**: 画像メッセージを追加
  - **689行目**: pendingImageをnullにリセット
  - **690行目**: 画像プレビューを非表示
  - **691行目**: ユーザー入力フィールドをクリア
**692-694行目**: 画像がない場合の処理
  - **693行目**: テキストメッセージを追加
**696行目**: 会話履歴を構築

##### セクション2: アシスタントメッセージ要素の準備（697-720行）

```javascript
      // Create a temporary assistant message element for streaming response
      const assistantMessageElement = document.createElement('div');
      assistantMessageElement.classList.add('message', 'assistant-message');
    
      const headerDiv = document.createElement('div');
      headerDiv.classList.add('message-header');
      headerDiv.textContent = 'Assistant';
      assistantMessageElement.appendChild(headerDiv);
    
      if (currentModel) {
        const modelDiv = document.createElement('div');
        modelDiv.classList.add('message-model');
        modelDiv.textContent = currentModel;
        assistantMessageElement.appendChild(modelDiv);
      }
    
      const assistantContentDiv = document.createElement('div');
      assistantContentDiv.classList.add('message-content');
      assistantMessageElement.appendChild(assistantContentDiv);
    
      chatContainer.appendChild(assistantMessageElement);
      chatContainer.scrollTop = chatContainer.scrollHeight;
```

**697行目**: コメント - ストリーミングレスポンス用の一時的なアシスタントメッセージ要素を作成
**698行目**: アシスタントメッセージ用のdiv要素を作成
**699行目**: メッセージのクラスを追加（`message`と`assistant-message`）
**701-704行目**: メッセージヘッダーの作成
  - **701行目**: ヘッダー用のdiv要素を作成
  - **702行目**: ヘッダーのクラスを`message-header`に設定
  - **703行目**: ヘッダーのテキストを「Assistant」に設定
  - **704行目**: ヘッダーをメッセージ要素に追加
**706-711行目**: モデル名の表示（モデルが選択されている場合）
  - **707行目**: モデル名表示用のdiv要素を作成
  - **708行目**: モデル名のクラスを`message-model`に設定
  - **709行目**: モデル名のテキストを設定
  - **710行目**: モデル名をメッセージ要素に追加
**713-715行目**: メッセージコンテンツの作成
  - **713行目**: コンテンツ用のdiv要素を作成
  - **714行目**: コンテンツのクラスを`message-content`に設定
  - **715行目**: コンテンツをメッセージ要素に追加
**717行目**: メッセージ要素をチャットコンテナに追加
**718行目**: チャットコンテナを最下部までスクロール

##### セクション3: リクエストの準備と送信（720-738行）

```javascript
      userInput.value = '';
      userInput.disabled = true;
      sendButton.disabled = true;
    
      const serverUrl = serverUrlInput.value.trim();
      const startTime = performance.now();
      let accumulatedText = '';
    
      try {
        const response = await fetch(`${serverUrl}/v1/chat/completions`, {
          method: 'POST',
          headers: { 'Content-Type': 'application/json' },
          body: JSON.stringify({
            model: currentModel,
            messages: conversationHistory,
            temperature: 0.7,
            max_tokens: -1,
            stream: true
          })
        });
        if (!response.ok) throw new Error('Server response was not ok');
```

**720行目**: ユーザー入力フィールドをクリア
**721行目**: ユーザー入力フィールドを一時的に無効化
**722行目**: 送信ボタンを一時的に無効化
**724行目**: サーバーURLを取得
**725行目**: 開始時刻を記録（パフォーマンス測定用）
**726行目**: 累積テキストを初期化
**728行目**: try-catchブロックの開始
**729-740行目**: POST /v1/chat/completions エンドポイントを呼び出し
  - **729行目**: fetchを使用してリクエストを送信（await）
  - **730行目**: HTTPメソッドをPOSTに設定
  - **731行目**: Content-Typeヘッダーをapplication/jsonに設定
  - **732-739行目**: リクエストボディを設定
    - **733行目**: モデルIDを設定
    - **734行目**: 会話履歴を設定
    - **735行目**: 温度パラメータを0.7に設定
    - **736行目**: 最大トークン数を-1に設定（無制限）
    - **737行目**: ストリーミングを有効化
**741行目**: レスポンスが正常でない場合、エラーをスロー


##### セクション4: ストリーミングレスポンスの処理（742-780行）

```javascript
        const reader = response.body.getReader();
        const decoder = new TextDecoder();
        let done = false;
        while (!done) {
          const { value, done: doneReading } = await reader.read();
          done = doneReading;
          if (value) {
            const chunk = decoder.decode(value, { stream: true });
            const lines = chunk.split('\n').filter(line => line.trim() !== '');
            for (const line of lines) {
              if (line.startsWith("data:")) {
                const dataStr = line.slice(5).trim();
                if (dataStr === "[DONE]") { done = true; break; }
                try {
                  const parsed = JSON.parse(dataStr);
                  const delta = parsed.choices[0].delta;
                  if (delta && delta.content) {
                    accumulatedText += delta.content;
                    assistantContentDiv.innerHTML = marked.parse(accumulatedText);
                    assistantMessageElement.querySelectorAll('pre code').forEach(block => {
                      hljs.highlightElement(block);
                    });
                    attachCopyButtons(assistantMessageElement);
                    if (typeof MathJax !== 'undefined') {
                      MathJax.typesetPromise([assistantMessageElement]).catch(err => console.error(err));
                    }
                  }
                } catch (err) {
                  console.error("Error parsing stream chunk", err);
                }
              } else if (line.startsWith("event:")) {
                const eventType = line.slice(6).trim();
                if (eventType === "error") {
                  console.error("Received error event from server:", line);
                  addMessage("Error: Received error event from server", false);
                  done = true;
                  break;
                }
              }
            }
          }
        }
```

**742行目**: レスポンスボディのリーダーを取得
**743行目**: TextDecoderを作成（バイナリデータをテキストに変換）
**744行目**: ストリーミング完了フラグを初期化
**745行目**: ストリーミングが完了するまでループ
**746行目**: リーダーからチャンクを読み取る（await）
**747行目**: 読み取りが完了したかどうかをdoneフラグに設定
**748行目**: チャンクが存在する場合
**749行目**: チャンクをテキストにデコード
**750行目**: チャンクを行に分割し、空行を除外
**751行目**: 各行に対して処理を実行
**752行目**: 行が「data:」で始まる場合
**753行目**: 「data:」プレフィックスを削除
**754行目**: データが「[DONE]」の場合、ストリーミングを終了
**755行目**: try-catchブロックの開始
**756行目**: データをJSONとしてパース
**757行目**: deltaオブジェクトを取得
**758行目**: deltaにcontentが含まれる場合
**759行目**: 累積テキストにcontentを追加
**760行目**: Marked.jsを使用してMarkdownをHTMLに変換し、コンテンツを更新
**761-763行目**: すべてのコードブロックにHighlight.jsを適用
**764行目**: コピーボタンを追加
**765-767行目**: MathJaxが利用可能な場合、数式をレンダリング
**768行目**: if文の終了（delta.contentのチェック）
**769-771行目**: JSON パースエラーのハンドリング
  - **770行目**: エラーをコンソールに出力
**772-781行目**: イベント行の処理
  - **772行目**: 行が「event:」で始まる場合
  - **773行目**: イベントタイプを取得
  - **774行目**: イベントタイプが「error」の場合
  - **775行目**: エラーをコンソールに出力
  - **776行目**: エラーメッセージを表示
  - **777行目**: ストリーミングを終了
  - **778行目**: ループを抜ける
**779行目**: if文の終了（イベントタイプのチェック）
**780行目**: if文の終了（行の開始文字列のチェック）
**781行目**: forループの終了
**782行目**: if文の終了（valueのチェック）
**783行目**: whileループの終了

##### セクション5: ストリーミング完了後の処理とエラーハンドリング（784-806行）

```javascript
        const endTime = performance.now();
        const timeElapsed = ((endTime - startTime) / 1000).toFixed(2);
        if (currentChat) {
          // Store the assistant message into the chat history
          currentChat.messages.push({ content: accumulatedText, isUser: false, isImage: false });
          if (currentChat.name.startsWith('Conversation')) {
            const snippet = accumulatedText.split(' ').slice(0, 7).join(' ');
            currentChat.name = snippet ? `Conversation: ${snippet}...` : currentChat.name;
            updateChatList();
          }
        }
      } catch (error) {
        console.error('Error:', error);
        addMessage('Error: Unable to get a response from the server. Please try again.', false);
        isConnected = false;
        updateConnectionStatus('Disconnected', false);
      } finally {
        userInput.disabled = false;
        sendButton.disabled = false;
        userInput.focus();
      }
    }
```

**784行目**: 終了時刻を記録
**785行目**: 経過時間を計算（秒単位、小数点以下2桁）
**786行目**: 現在のチャットが存在する場合
**787行目**: コメント - アシスタントメッセージをチャット履歴に保存
**788行目**: メッセージをチャット履歴に追加
**789行目**: チャット名がデフォルト名（「Conversation」で始まる）の場合
**790行目**: 会話内容の最初の7単語を取得
**791行目**: チャット名を更新（スニペット + 「...」）
**792行目**: チャットリストを更新
**793行目**: if文の終了（チャット名のチェック）
**794行目**: if文の終了（currentChatのチェック）
**795-800行目**: エラーハンドリング
  - **796行目**: エラーをコンソールに出力
  - **797行目**: エラーメッセージを表示
  - **798行目**: 接続状態をfalseに設定
  - **799行目**: 接続ステータスを「Disconnected」に更新
**801-805行目**: finally ブロック（必ず実行される処理）
  - **802行目**: ユーザー入力フィールドを有効化
  - **803行目**: 送信ボタンを有効化
  - **804行目**: ユーザー入力フィールドにフォーカス
**806行目**: 関数の終了


#### 807-849行: イベントリスナーの設定

##### 807-823行: 画像アップロード関連のイベントリスナー

```javascript
    // Image upload: store image and show preview.
    uploadButton.addEventListener('click', () => { imageUpload.click(); });
    imageUpload.addEventListener('change', () => {
      const file = imageUpload.files[0];
      if (!file) return;
      const reader = new FileReader();
      reader.onload = (e) => {
        pendingImage = e.target.result;
        imagePreview.innerHTML = `<img src="${pendingImage}" style="max-width:100%; border-radius: var(--border-radius);" />`;
        imagePreview.style.display = "block";
      };
      reader.readAsDataURL(file);
      imageUpload.value = "";
    });
```

**807行目**: コメント - 画像アップロード: 画像を保存してプレビューを表示
**808行目**: 画像アップロードボタンのクリックイベントリスナー（ファイル選択ダイアログを開く）
**809行目**: 画像ファイル選択inputの変更イベントリスナー
**810行目**: 選択されたファイルを取得
**811行目**: ファイルが選択されていない場合、関数を終了
**812行目**: FileReaderを作成
**813行目**: ファイル読み込み完了時のイベントハンドラーを設定
**814行目**: 読み込んだ画像データ（Base64）をpendingImageに保存
**815行目**: 画像プレビューを表示（imgタグを含むHTML文字列）
**816行目**: 画像プレビューを表示状態に変更
**817行目**: イベントハンドラーの終了
**818行目**: ファイルをBase64データURLとして読み込む
**819行目**: ファイル選択inputの値をクリア（同じファイルを再度選択できるようにする）
**820行目**: イベントリスナーの終了

##### 821-841行: 接続ボタンのイベントリスナー

```javascript
    connectButton.addEventListener('click', () => {
      if (isConnected) {
        isConnected = false;
        updateConnectionStatus('Disconnected', false);
        userInput.disabled = true;
        sendButton.disabled = true;
        addMessage('Disconnected from LM Studio server.', false);
        currentModel = '';
        modelSelect.disabled = true;
      } else {
        connectToServer();
      }
    });
```

**821行目**: 接続ボタンのクリックイベントリスナー
**822行目**: 接続されている場合
**823行目**: 接続状態をfalseに設定
**824行目**: 接続ステータスを「Disconnected」に更新
**825行目**: ユーザー入力フィールドを無効化
**826行目**: 送信ボタンを無効化
**827行目**: 切断メッセージを表示
**828行目**: 現在のモデルをクリア
**829行目**: モデル選択ドロップダウンを無効化
**830-832行目**: 接続されていない場合
  - **831行目**: サーバーに接続
**833行目**: イベントリスナーの終了

##### 834-849行: その他のイベントリスナーと初期化

```javascript
    userInput.addEventListener('keypress', (e) => { if (e.key === 'Enter') sendMessage(); });
    sendButton.addEventListener('click', sendMessage);
    newChatButton.addEventListener('click', () => { createNewChat(); });
    toggleSidebarButton.addEventListener('click', () => { chatSidebar.classList.toggle('collapsed'); });
    
    serverUrlInput.focus();
  </script>
</body>
</html>
```

**834行目**: ユーザー入力フィールドのキープレスイベントリスナー（Enterキーが押されたらメッセージを送信）
**835行目**: 送信ボタンのクリックイベントリスナー（メッセージを送信）
**836行目**: 新規チャットボタンのクリックイベントリスナー（新しいチャットを作成）
**837行目**: サイドバートグルボタンのクリックイベントリスナー（サイドバーの折りたたみ/展開を切り替え）
**839行目**: サーバーURL入力フィールドに初期フォーカスを設定
**840行目**: scriptタグの終了
**841行目**: bodyタグの終了
**842行目**: htmlタグの終了

---

## まとめ

このドキュメントでは、LM Studio Chat WebUIの`index.html`ファイルの全919行を詳細に解説しました。

### 主要な機能

1. **サーバー接続管理**
   - LM Studio APIサーバーへの接続/切断
   - 接続ステータスの表示
   - サーバーアドレスとモデルの永続化（localStorage）

2. **モデル選択と管理**
   - 利用可能なモデル一覧の取得
   - モデルの切り替えとアンロード
   - 選択したモデルの自動保存と復元

3. **チャットセッション管理**
   - 複数のチャットセッションの作成、切り替え、削除
   - チャット名の自動更新
   - メモリ内でのチャット履歴管理

4. **メッセージ送受信**
   - テキストメッセージの送信
   - ストリーミングレスポンスのリアルタイム表示
   - メッセージのコピー機能

5. **ビジョンモデル対応**
   - 画像のアップロードとBase64エンコード
   - 画像とテキストを組み合わせたメッセージ送信

6. **レンダリング機能**
   - Markdownレンダリング（Marked.js）
   - コードブロックのシンタックスハイライト（Highlight.js）
   - LaTeX数式レンダリング（MathJax）

7. **UI/UX機能**
   - レスポンシブデザイン（モバイル対応）
   - サイドバーの折りたたみ機能
   - ダークモード（紫テーマ）
   - アニメーション効果

### 技術スタック

- **HTML5**: 単一ファイルアプリケーション
- **CSS3**: カスタム紫テーマ、CSS変数、レスポンシブデザイン
- **Vanilla JavaScript (ES6+)**: フレームワークなし、モダンなJavaScript構文
- **外部ライブラリ**:
  - Marked.js: Markdownレンダリング
  - Highlight.js: コードシンタックスハイライト
  - MathJax: LaTeX数式レンダリング
  - Font Awesome: アイコン
- **Web APIs**:
  - Fetch API: サーバー通信
  - Clipboard API: コピー機能
  - FileReader API: 画像読み込み
  - localStorage API: 設定の永続化

### アーキテクチャパターン

このアプリケーションは、クライアントサイドMVCパターンを採用しています：

- **Model**: JavaScriptオブジェクト（chats配列、currentChatオブジェクト）
- **View**: DOM要素とそのレンダリングロジック
- **Controller**: イベントハンドラーとビジネスロジック関数

### セキュリティとパフォーマンス

- **CORS対応**: LM Studio APIサーバーとの通信
- **エラーハンドリング**: 接続エラー、ストリーミングエラー、レンダリングエラーの適切な処理
- **ストリーミング処理**: リアルタイムでのレスポンス表示
- **メモリ管理**: チャット履歴のメモリ内管理（将来的にはIndexedDBへの永続化を検討）

このファイルは、モダンなWeb技術を活用した、完全に機能するチャットアプリケーションの優れた例です。

### ファイル構成の概要

1. **HTMLヘッダー（1-449行）**
   - メタタグとタイトル
   - 外部リソース（Google Fonts、Font Awesome、Highlight.js CSS）
   - CSSスタイル（CSS変数、レイアウト、コンポーネントスタイル、レスポンシブデザイン、アニメーション）
   - JavaScriptライブラリの設定（Marked.js、Highlight.js、MathJax）

2. **HTMLボディ（450-490行）**
   - アプリケーション構造（ヘッダー、サイドバー、チャット領域、入力コンテナ）
   - コンテキストメニュー

3. **JavaScriptコード（491-849行）**
   - グローバル変数とDOM要素の取得
   - ヘルパー関数（メッセージ追加、チャット管理、会話履歴構築など）
   - メイン関数（サーバー接続、メッセージ送信、ストリーミング処理）
   - イベントリスナー（ユーザーインタラクション処理）

### 主要な機能

- **サーバー接続管理**: LM Studio APIサーバーへの接続/切断
- **モデル選択**: 利用可能なモデルの一覧から選択
- **チャット管理**: 複数のチャットセッションの作成、切り替え、削除
- **メッセージ送受信**: テキストと画像のメッセージ送信、ストリーミングレスポンス受信
- **レンダリング**: Markdown、LaTeX数式、コードブロックのシンタックスハイライト
- **UI機能**: サイドバーの折りたたみ、レスポンシブデザイン、アニメーション

このアプリケーションは、単一のHTMLファイルで完結した、フル機能のWebアプリケーションです。外部ライブラリをCDNから読み込むことで、依存関係の管理を簡素化し、どのデバイスでも簡単に実行できるように設計されています。

