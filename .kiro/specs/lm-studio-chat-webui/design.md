# 設計書

## 概要

LM Studio Chat WebUIは、単一のHTMLファイルで構成されたスタンドアロンWebアプリケーションです。Vanilla JavaScript（ES6+）を使用し、外部ライブラリ（Marked.js、Highlight.js、MathJax、Font Awesome）を活用して、ローカルでホストされているLM Studio APIサーバーとの対話を実現します。

アプリケーションは、サーバー接続管理、モデル選択、複数チャットセッションの管理、リアルタイムストリーミングレスポンス、画像アップロード対応、Markdown/LaTeX/コードハイライトのレンダリング機能を提供します。

## アーキテクチャ

### アーキテクチャパターン

本アプリケーションは、**クライアントサイドMVCパターン**を採用しています：

- **Model**: JavaScriptオブジェクトとして表現されるデータ構造（chats配列、currentChatオブジェクト）
- **View**: DOM要素とそのレンダリングロジック
- **Controller**: イベントハンドラーとビジネスロジック関数

### システム構成

```
┌─────────────────────────────────────────────────────────┐
│                    Browser (Client)                      │
│  ┌───────────────────────────────────────────────────┐  │
│  │           LM Studio Chat WebUI (SPA)              │  │
│  │  ┌─────────────┐  ┌──────────────┐  ┌─────────┐  │  │
│  │  │   UI Layer  │  │ Business     │  │  Data   │  │  │
│  │  │  (DOM/CSS)  │←→│ Logic Layer  │←→│  Layer  │  │  │
│  │  └─────────────┘  └──────────────┘  └─────────┘  │  │
│  │         ↓                                          │  │
│  │  ┌─────────────────────────────────────────────┐  │  │
│  │  │  External Libraries                         │  │  │
│  │  │  - Marked.js (Markdown)                     │  │  │
│  │  │  - Highlight.js (Syntax Highlighting)       │  │  │
│  │  │  - MathJax (LaTeX Rendering)                │  │  │
│  │  └─────────────────────────────────────────────┘  │  │
│  └───────────────────────────────────────────────────┘  │
│                          ↓ HTTP/Fetch API                │
└──────────────────────────┼──────────────────────────────┘
                           ↓
┌──────────────────────────┼──────────────────────────────┐
│                  LM Studio API Server                    │
│  ┌────────────────────────────────────────────────────┐ │
│  │  REST API Endpoints:                               │ │
│  │  - GET  /v1/models                                 │ │
│  │  - POST /v1/chat/completions (streaming)          │ │
│  │  - POST /v1/model/eject                           │ │
│  └────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────┘
```

### レイヤー構成

1. **UIレイヤー**: DOM要素、CSS スタイリング、アニメーション
2. **ビジネスロジックレイヤー**: イベントハンドラー、API通信、データ変換
3. **データレイヤー**: インメモリデータストア（chats配列）

## コンポーネントとインターフェース

### 1. サーバー接続マネージャー

**責務**: LM Studio APIサーバーとの接続を管理

**主要関数**:
```javascript
async function connectToServer()
```
- サーバーURL検証
- GET /v1/models エンドポイント呼び出し
- モデル一覧の取得とドロップダウン更新
- 接続ステータスの更新
- エラーハンドリング

**状態管理**:
- `isConnected`: boolean - 接続状態
- `currentModel`: string - 現在選択されているモデルID

**DOM要素**:
- `serverUrlInput`: サーバーアドレス入力フィールド
- `connectButton`: 接続/切断ボタン
- `connectionStatus`: 接続ステータス表示
- `modelSelect`: モデル選択ドロップダウン

### 2. モデルマネージャー

**責務**: AIモデルの選択とライフサイクル管理

**主要関数**:
```javascript
async function ejectCurrentModel(oldModel)
```
- POST /v1/model/eject エンドポイント呼び出し
- モデルのアンロード処理

**イベントハンドラー**:
```javascript
modelSelect.addEventListener('change', async (e) => {...})
```
- モデル変更時の処理
- 旧モデルのアンロード
- 新モデルの設定

### 3. チャットセッションマネージャー

**責務**: 複数のチャットセッションの作成、切り替え、削除

**データ構造**:
```javascript
// Chat Session Object
{
  id: number,           // タイムスタンプベースのユニークID
  name: string,         // チャット名（デフォルト: "Conversation N"）
  messages: Array<{     // メッセージ配列
    content: string,    // メッセージ内容（HTML）
    isUser: boolean,    // ユーザーメッセージかどうか
    metrics: string,    // パフォーマンスメトリクス（オプション）
    isImage: boolean,   // 画像メッセージかどうか
    imageData: string,  // Base64画像データ（画像メッセージの場合）
    text: string        // 画像に付随するテキスト（画像メッセージの場合）
  }>
}
```

**主要関数**:
```javascript
function createNewChat()
```
- 新しいチャットセッションオブジェクトの作成
- chats配列への追加
- currentChatの更新
- UIの更新

```javascript
function updateChatList()
```
- サイドバーのチャット一覧を再レンダリング
- アクティブチャットのハイライト
- イベントリスナーのアタッチ

```javascript
function loadChat(chat)
```
- 指定されたチャットのメッセージ履歴を表示
- chatContainerのクリアと再構築

```javascript
function deleteChat(chatId)
```
- 指定されたチャットをchats配列から削除
- 削除されたチャットがアクティブな場合、別のチャットをアクティブ化
- すべてのチャットが削除された場合、新しいチャットを自動作成

**状態管理**:
- `chats`: Array<ChatSession> - すべてのチャットセッション
- `currentChat`: ChatSession | null - 現在アクティブなチャット

### 4. メッセージマネージャー

**責務**: メッセージの送受信、表示、履歴管理

**主要関数**:
```javascript
async function sendMessage()
```
- ユーザー入力の取得と検証
- 画像メッセージの処理
- 会話履歴の構築
- API リクエストの送信
- ストリーミングレスポンスの処理

```javascript
function addMessage(content, isUser, metrics = null, store = true)
```
- メッセージDOMの作成
- Markdownレンダリング
- シンタックスハイライト適用
- MathJaxレンダリング
- コピーボタンのアタッチ
- チャット履歴への保存（storeがtrueの場合）

```javascript
function addImageMessage(dataURL, promptText)
```
- 画像メッセージの表示
- メッセージ履歴への画像データ保存

```javascript
function buildConversationHistory()
```
- システムプロンプトの決定（画像含む場合とテキストのみの場合）
- メッセージ配列の構築（role: user/assistant）
- 画像メッセージの適切なフォーマット化

### 5. ストリーミングレスポンスハンドラー

**責務**: サーバーからのストリーミングレスポンスをリアルタイムで処理

**処理フロー**:
1. ReadableStreamリーダーの取得
2. TextDecoderによるバイナリデータのデコード
3. "data:"プレフィックスを持つ行の抽出
4. JSON パースとdelta.contentの取得
5. 累積テキストの更新
6. Markdownレンダリングとシンタックスハイライトの再適用
7. "[DONE]"シグナルの検出とストリーム終了

**実装詳細**:
```javascript
const reader = response.body.getReader();
const decoder = new TextDecoder();
let accumulatedText = '';

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
        const parsed = JSON.parse(dataStr);
        const delta = parsed.choices[0].delta;
        if (delta && delta.content) {
          accumulatedText += delta.content;
          // リアルタイム更新
        }
      }
    }
  }
}
```

### 6. レンダリングエンジン

**責務**: Markdown、LaTeX、コードブロックの適切なレンダリング

#### Markdownレンダリング（Marked.js）

**設定**:
```javascript
marked.setOptions({
  gfm: true,              // GitHub Flavored Markdown
  breaks: true,           // 改行を<br>に変換
  smartypants: true,      // スマート引用符
  headerIds: false,       // ヘッダーIDを生成しない
  highlight: function(code, lang) {
    // Highlight.jsとの統合
  }
});
```

**使用箇所**:
- `addMessage()`関数内: `contentDiv.innerHTML = marked.parse(content)`

#### シンタックスハイライト（Highlight.js）

**テーマ**: Atom One Dark

**適用方法**:
```javascript
messageDiv.querySelectorAll('pre code').forEach(block => {
  hljs.highlightElement(block);
});
```

**コピーボタン機能**:
```javascript
function attachCopyButtons(container)
```
- すべての`<pre>`要素にコピーボタンを追加
- クリップボードAPIを使用してコードをコピー
- 視覚的フィードバック（"Copied!"表示）

#### LaTeX数式レンダリング（MathJax）

**設定**:
```javascript
window.MathJax = {
  tex: {
    inlineMath: [['$', '$'], ['\\(', '\\)']],
    displayMath: [['$$', '$$'], ['\\[', '\\]']],
    processEscapes: true
  },
  options: {
    skipHtmlTags: ['script', 'noscript', 'style', 'textarea', 'pre']
  }
};
```

**レンダリング**:
```javascript
MathJax.typesetPromise([messageDiv])
  .catch(err => console.error('MathJax typeset failed:', err));
```

### 7. 画像アップロードハンドラー

**責務**: 画像ファイルの選択、読み込み、プレビュー表示

**処理フロー**:
1. ファイル選択ダイアログの表示
2. FileReaderによる画像読み込み
3. Base64データURLへの変換
4. プレビュー表示
5. pendingImage変数への保存
6. メッセージ送信時の画像データ統合

**実装**:
```javascript
uploadButton.addEventListener('click', () => { imageUpload.click(); });

imageUpload.addEventListener('change', () => {
  const file = imageUpload.files[0];
  if (!file) return;
  const reader = new FileReader();
  reader.onload = (e) => {
    pendingImage = e.target.result;
    imagePreview.innerHTML = `<img src="${pendingImage}" ... />`;
    imagePreview.style.display = "block";
  };
  reader.readAsDataURL(file);
});
```

### 8. UIコントローラー

**責務**: ユーザーインタラクションの処理とUI状態の管理

**主要機能**:

#### サイドバートグル
```javascript
toggleSidebarButton.addEventListener('click', () => {
  chatSidebar.classList.toggle('collapsed');
});
```
- CSSクラスの切り替えによる幅変更（250px ↔ 40px）
- トランジションアニメーション

#### コンテキストメニュー
```javascript
function showContextMenu(x, y, chatId)
function hideContextMenu()
```
- 右クリックによるコンテキストメニュー表示
- チャット削除機能

#### 接続ステータス更新
```javascript
function updateConnectionStatus(message, connected)
```
- ステータステキストの更新
- 色の変更（接続: accent-color、切断: #f44336）
- ボタンテキストの切り替え（Connect ↔ Disconnect）
- 入力フィールドの有効/無効化

## データモデル

### ChatSession
```typescript
interface ChatSession {
  id: number;           // ユニークID（タイムスタンプ）
  name: string;         // チャット名
  messages: Message[];  // メッセージ配列
}
```

### Message
```typescript
interface Message {
  content: string;      // メッセージ内容（HTML）
  isUser: boolean;      // ユーザーメッセージフラグ
  metrics?: string;     // パフォーマンスメトリクス
  isImage: boolean;     // 画像メッセージフラグ
  imageData?: string;   // Base64画像データ
  text?: string;        // 画像に付随するテキスト
}
```

### API Request/Response

#### GET /v1/models
**Response**:
```typescript
{
  data: Array<{
    id: string;         // モデルID
    // その他のモデル情報
  }>
}
```

#### POST /v1/chat/completions
**Request**:
```typescript
{
  model: string;
  messages: Array<{
    role: 'system' | 'user' | 'assistant';
    content: string | Array<{
      type: 'text' | 'image_url';
      text?: string;
      image_url?: { url: string };
    }>;
  }>;
  temperature: number;
  max_tokens: number;
  stream: boolean;
}
```

**Streaming Response**:
```
data: {"choices":[{"delta":{"content":"..."}}]}
data: {"choices":[{"delta":{"content":"..."}}]}
...
data: [DONE]
```

#### POST /v1/model/eject
**Request**:
```typescript
{
  model: string;        // アンロードするモデルID
}
```

## 正確性プロパティ


プロパティとは、システムのすべての有効な実行において真であるべき特性や動作のことです。本質的には、システムが何をすべきかについての形式的な記述です。プロパティは、人間が読める仕様と機械で検証可能な正確性保証との橋渡しとなります。

### プロパティ1: 接続成功時のモデル一覧取得

*任意の*有効なLM StudioサーバーURLに対して、接続が成功した場合、システムは利用可能なモデルの一覧を取得し、ドロップダウンメニューに表示する必要があります。

**検証: 要件 1.2, 2.1, 2.2**

### プロパティ2: 接続失敗時のエラー表示

*任意の*無効なサーバーURLまたは到達不可能なサーバーに対して、システムは「Failed to connect」ステータスを表示し、適切なエラーメッセージをユーザーに提示する必要があります。

**検証: 要件 1.3, 15.1**

### プロパティ3: 接続状態とUI要素の有効化

*任意の*時点において、システムが接続されていない場合、メッセージ入力フィールドと送信ボタンは無効化されている必要があります。

**検証: 要件 1.5**

### プロパティ4: モデル切り替え時のアンロード

*任意の*モデルから別のモデルへの切り替え時に、システムは現在ロードされているモデルに対してPOST /v1/model/eject エンドポイントを呼び出す必要があります。

**検証: 要件 2.3, 2.4**

### プロパティ5: チャット作成時のデフォルト名

*任意の*新しいチャットセッションが作成される際、システムはデフォルト名「Conversation N」（Nはチャット番号）を割り当てる必要があります。

**検証: 要件 3.2**

### プロパティ6: チャット削除後のアクティブチャット管理

*任意の*チャットセッションが削除される際、削除されたチャットが現在アクティブな場合、システムは別のチャットをアクティブにするか、すべてのチャットが削除された場合は新しいチャットを自動作成する必要があります。

**検証: 要件 3.5, 3.7**

### プロパティ7: チャット名の自動更新

*任意の*チャットセッションにおいて、アシスタントが最初のメッセージを送信した際、デフォルト名「Conversation」で始まる場合、システムはチャット名を会話内容の最初の7単語に基づいて更新する必要があります。

**検証: 要件 3.6**

### プロパティ8: 空メッセージの送信防止

*任意の*空文字列または空白のみで構成された文字列に対して、システムはメッセージの送信を防止する必要があります。

**検証: 要件 4.5**

### プロパティ9: 会話履歴の構築

*任意の*メッセージ送信時に、システムはシステムプロンプトで始まり、すべてのメッセージを送信順序で含み、各メッセージに適切なロール（user/assistant）を割り当てた会話履歴を構築する必要があります。

**検証: 要件 5.1, 5.4**

### プロパティ10: 画像メッセージのシステムプロンプト

*任意の*会話履歴において、画像メッセージが1つでも含まれる場合、システムプロンプトは「You are an AI assistant that analyzes images.」である必要があります。

**検証: 要件 5.2**

### プロパティ11: テキストのみのシステムプロンプト

*任意の*会話履歴において、画像メッセージが含まれない場合、システムプロンプトは「You are an intelligent assistant. You always provide well-reasoned answers that are both correct and helpful.」である必要があります。

**検証: 要件 5.3**

### プロパティ12: 画像メッセージのフォーマット

*任意の*画像メッセージに対して、会話履歴に追加される際、コンテンツ配列にはテキストオブジェクトとimage_urlオブジェクトの両方が含まれる必要があります。

**検証: 要件 5.5**

### プロパティ13: 画像なしテキストのデフォルトプロンプト

*任意の*画像アップロード後のメッセージ送信において、テキスト入力が空の場合、システムはデフォルトプロンプト「What's in this image?」を使用する必要があります。

**検証: 要件 6.4**

### プロパティ14: Markdownレンダリング

*任意の*アシスタントメッセージに対して、システムはMarked.jsライブラリを使用してMarkdown構文をHTMLに変換する必要があります。

**検証: 要件 7.1**

### プロパティ15: GFMサポート

*任意の*GitHub Flavored Markdown構文（テーブル、タスクリストなど）を含むメッセージに対して、システムは適切にレンダリングする必要があります。

**検証: 要件 7.6**

### プロパティ16: 改行の自動変換

*任意の*改行文字を含むメッセージに対して、システムは改行を<br>タグに自動変換する必要があります。

**検証: 要件 7.7**

### プロパティ17: コードブロックのシンタックスハイライト

*任意の*コードブロックを含むメッセージに対して、システムはHighlight.jsライブラリを使用してシンタックスハイライトを適用する必要があります。

**検証: 要件 8.1**

### プロパティ18: 言語別シンタックスハイライト

*任意の*言語指定を持つコードブロックに対して、システムはその言語の構文規則に従ってハイライトを適用する必要があります。

**検証: 要件 8.2**

### プロパティ19: コピーボタンの追加

*任意の*コードブロックがレンダリングされる際、システムは「Copy」ボタンを右上に追加する必要があります。

**検証: 要件 8.4**

### プロパティ20: コピーボタンのフィードバック

*任意の*コピーボタンがクリックされた際、システムはコードをクリップボードにコピーし、ボタンテキストを一時的に「Copied!」に変更し、2秒後に「Copy」に戻す必要があります。

**検証: 要件 8.5, 8.6**

### プロパティ21: LaTeX数式レンダリング

*任意の*LaTeX数式を含むメッセージに対して、システムはMathJaxライブラリを使用してレンダリングする必要があります。

**検証: 要件 9.1**

### プロパティ22: インライン数式構文サポート

*任意の*インライン数式に対して、システムは$...$と\\(...\\)の両方の構文をサポートする必要があります。

**検証: 要件 9.2**

### プロパティ23: ディスプレイ数式構文サポート

*任意の*ディスプレイ数式に対して、システムは$$...$$と\\[...\\]の両方の構文をサポートする必要があります。

**検証: 要件 9.3**

### プロパティ24: ストリーミングリクエストパラメータ

*任意の*チャット補完リクエストに対して、システムはstream: trueパラメータを含める必要があります。

**検証: 要件 10.1**

### プロパティ25: ストリーミングチャンクの解析

*任意の*ストリーミングレスポンスチャンクに対して、システムは"data:"で始まる行を解析し、delta.contentを抽出する必要があります。

**検証: 要件 10.3, 10.4**

### プロパティ26: ストリーミング終了処理

*任意の*ストリーミングレスポンスにおいて、"[DONE]"シグナルを受信した際、システムはストリーミングを終了し、最終メッセージをチャット履歴に保存する必要があります。

**検証: 要件 10.5**

### プロパティ27: ストリーミング中のレンダリング更新

*任意の*ストリーミングチャンク更新時に、システムはMarkdownレンダリング、シンタックスハイライト、数式レンダリングを再適用する必要があります。

**検証: 要件 10.7**

### プロパティ28: サイドバートグル

*任意の*トグルボタンクリック時に、システムはサイドバーの幅を250pxと40pxの間で切り替え、コンテンツの表示/非表示を制御する必要があります。

**検証: 要件 11.1, 11.2, 11.3**

### プロパティ29: レスポンシブサイドバー

*任意の*画面幅が480px以下の場合、システムはサイドバーを非表示にする必要があります。

**検証: 要件 12.1**

### プロパティ30: レスポンシブメッセージ幅

*任意の*モバイルビュー（画面幅480px以下）において、メッセージの最大幅は90%である必要があります。

**検証: 要件 12.2**

### プロパティ31: レスポンシブレイアウト

*任意の*モバイルビュー（画面幅480px以下）において、サーバーURL入力、モデル選択、接続ボタンは縦に配置される必要があります。

**検証: 要件 12.3**

### プロパティ32: タッチフレンドリーなUI要素

*任意の*インタラクティブ要素に対して、最小サイズは44x44pxである必要があります。

**検証: 要件 12.5**

### プロパティ33: メッセージアニメーション

*任意の*新しいメッセージが追加される際、システムは0.3秒のフェードインアニメーションを適用する必要があります。

**検証: 要件 13.6**

### プロパティ34: パフォーマンスメトリクスの計測

*任意の*メッセージ送信において、システムはperformance.now()を使用して開始時刻と終了時刻を記録し、経過時間を秒単位で小数点以下2桁まで計算する必要があります。

**検証: 要件 14.1, 14.2, 14.3**

### プロパティ35: エラーメッセージの表示

*任意の*API呼び出しエラーに対して、システムは適切なエラーメッセージをユーザーに表示し、エラーをコンソールに記録する必要があります。

**検証: 要件 15.1, 15.2, 15.3, 15.4, 15.5, 15.6**

### プロパティ36: チャットセッションのメモリ保存

*任意の*チャットセッションとメッセージに対して、システムはメモリ内の配列に保存し、チャット切り替え時に正しく読み込む必要があります。

**検証: 要件 16.1, 16.2, 16.3**

### プロパティ37: 接続メッセージの非保存

*任意の*接続/切断メッセージに対して、システムはそれをチャット履歴に保存しない必要があります。

**検証: 要件 16.5**

### プロパティ38: 初期フォーカス設定

*任意の*ページ読み込み時に、システムはサーバーURL入力フィールドに自動的にフォーカスを設定する必要があります。

**検証: 要件 17.1**

### プロパティ39: メッセージ送信後のフォーカス復帰

*任意の*メッセージ送信完了時に、システムは入力フィールドに自動的にフォーカスを戻す必要があります。

**検証: 要件 17.2**

### プロパティ40: 自動スクロール

*任意の*新しいメッセージが追加される際、システムはチャットコンテナを自動的に最下部までスクロールする必要があります。

**検証: 要件 17.3**

### プロパティ41: Enterキーによるメッセージ送信

*任意の*入力フィールドにフォーカスがある状態でEnterキーが押された際、システムはメッセージを送信する必要があります。

**検証: 要件 17.4**

### プロパティ42: ホバー効果

*任意の*インタラクティブ要素に対して、ホバー時にシステムは色を変更してインタラクティブ性を示す必要があります。

**検証: 要件 17.6**

## エラーハンドリング

### エラーカテゴリ

1. **ネットワークエラー**
   - サーバー接続失敗
   - タイムアウト
   - ネットワーク切断

2. **APIエラー**
   - 無効なレスポンス
   - ストリーミングエラー
   - モデルアンロードエラー

3. **クライアントサイドエラー**
   - ファイル読み込みエラー
   - レンダリングエラー（MathJax、Highlight.js）
   - クリップボードAPIエラー

### エラーハンドリング戦略

#### ネットワークエラー
```javascript
try {
  const response = await fetch(url);
  if (!response.ok) throw new Error('Server response was not ok');
  // 処理続行
} catch (error) {
  console.error('Error:', error);
  updateConnectionStatus('Failed to connect', false);
  addMessage('Error: Unable to connect to the LM Studio server...', false);
}
```

#### ストリーミングエラー
```javascript
if (line.startsWith("event:")) {
  const eventType = line.slice(6).trim();
  if (eventType === "error") {
    console.error("Received error event from server:", line);
    addMessage("Error: Received error event from server", false);
    done = true;
    break;
  }
}
```

#### レンダリングエラー
```javascript
MathJax.typesetPromise([messageDiv])
  .catch(err => console.error('MathJax typeset failed:', err));
```

### エラーリカバリー

1. **接続エラー**: ユーザーに再接続を促す
2. **ストリーミングエラー**: 現在のメッセージを破棄し、UI状態をリセット
3. **レンダリングエラー**: 元のテキストを表示し、エラーをログ
4. **モデルアンロードエラー**: エラーをログし、処理を続行

## テスト戦略

### デュアルテストアプローチ

本アプリケーションのテストは、**ユニットテスト**と**プロパティベーステスト**の両方を使用します：

- **ユニットテスト**: 特定の例、エッジケース、エラー条件を検証
- **プロパティベーステスト**: すべての入力にわたる普遍的なプロパティを検証

両者は補完的であり、包括的なカバレッジに必要です。

### ユニットテストの焦点

ユニットテストは以下に焦点を当てます：

1. **特定の例**
   - 接続成功時のモデル一覧表示
   - 特定のMarkdown構文のレンダリング
   - 画像アップロードフロー

2. **エッジケース**
   - すべてのチャットが削除された場合の自動チャット作成
   - 空メッセージの送信防止
   - 画像のみでテキストがない場合のデフォルトプロンプト

3. **エラー条件**
   - 無効なサーバーURLでの接続失敗
   - ストリーミング中のエラーイベント
   - MathJaxレンダリング失敗

4. **統合ポイント**
   - 外部ライブラリ（Marked.js、Highlight.js、MathJax）との統合
   - Fetch APIとの統合
   - Clipboard APIとの統合

### プロパティベーステストの焦点

プロパティベーステストは以下に焦点を当てます：

1. **不変条件**
   - 接続状態とUI要素の有効化状態の一貫性
   - チャット削除後のアクティブチャット存在保証
   - メッセージ履歴の順序保持

2. **ラウンドトリッププロパティ**
   - チャット作成→保存→読み込み→同一性
   - メッセージ送信→履歴保存→読み込み→同一性

3. **メタモルフィックプロパティ**
   - 任意のMarkdown入力→HTML出力は有効なHTML
   - 任意のコードブロック→シンタックスハイライト適用
   - 任意のLaTeX数式→MathJaxレンダリング

4. **エラー条件**
   - 任意の無効な入力→適切なエラーメッセージ
   - 任意のネットワークエラー→UI状態のリセット

### プロパティベーステスト設定

**推奨ライブラリ**: 
- JavaScript: **fast-check**

**設定**:
- 各プロパティテストは最低100回の反復を実行
- 各テストは設計書のプロパティを参照するコメントタグを含む
- タグ形式: `// Feature: lm-studio-chat-webui, Property N: [プロパティテキスト]`

**例**:
```javascript
import fc from 'fast-check';

// Feature: lm-studio-chat-webui, Property 8: 空メッセージの送信防止
test('空メッセージの送信防止', () => {
  fc.assert(
    fc.property(
      fc.string().filter(s => s.trim() === ''),
      (emptyMessage) => {
        const result = validateMessage(emptyMessage);
        expect(result).toBe(false);
      }
    ),
    { numRuns: 100 }
  );
});
```

### テストカバレッジ目標

- **ユニットテスト**: 主要な関数とイベントハンドラーの80%以上
- **プロパティベーステスト**: すべての正確性プロパティ（42個）
- **統合テスト**: 主要なユーザーフロー（接続、メッセージ送信、チャット管理）

### モックとスタブ

テストでは以下をモック化します：

1. **Fetch API**: サーバーレスポンスのシミュレーション
2. **FileReader API**: 画像アップロードのシミュレーション
3. **Clipboard API**: コピー機能のテスト
4. **外部ライブラリ**: Marked.js、Highlight.js、MathJaxの動作確認

### テスト実行環境

- **ブラウザ環境**: Jest + jsdom または Vitest
- **E2Eテスト**: Playwright または Cypress（オプション）

## 実装上の注意事項

### パフォーマンス最適化

1. **ストリーミングレスポンス**: チャンクごとにレンダリングを更新するため、大量のDOM操作が発生する可能性があります。必要に応じてデバウンスを検討してください。

2. **MathJaxレンダリング**: 数式が多い場合、レンダリングに時間がかかる可能性があります。非同期処理を適切に管理してください。

3. **メモリ管理**: チャット履歴が増えるとメモリ使用量が増加します。将来的にはローカルストレージやIndexedDBへの永続化を検討してください。

### セキュリティ考慮事項

1. **XSS対策**: Markdownレンダリング時にHTMLサニタイゼーションを検討してください（現在はMarked.jsのデフォルト設定に依存）。

2. **CORS**: LM Studio APIサーバーがCORSを適切に設定していることを確認してください。

3. **画像データ**: Base64エンコードされた画像データは大きくなる可能性があります。サイズ制限を検討してください。

### アクセシビリティ

1. **キーボードナビゲーション**: すべてのインタラクティブ要素がキーボードでアクセス可能であることを確認してください。

2. **スクリーンリーダー**: ARIA属性を追加して、スクリーンリーダーのサポートを向上させることを検討してください。

3. **カラーコントラスト**: 現在の紫テーマがWCAG 2.1 AA基準を満たしていることを確認してください。

### 将来の拡張性

1. **データ永続化**: LocalStorageまたはIndexedDBを使用したチャット履歴の永続化

2. **エクスポート機能**: チャット履歴のエクスポート（JSON、Markdown、PDF）

3. **カスタマイズ**: テーマのカスタマイズ、フォントサイズ調整

4. **マルチモーダル拡張**: 音声入力、ファイルアップロード（PDF、テキストファイル）

5. **プラグインシステム**: カスタムレンダラーやプロセッサーの追加


### プロパティ43: メッセージコピーボタンの追加

*任意の*メッセージが表示される際、システムは各メッセージにコピーボタンを追加する必要があります。

**検証: 要件 18.1**

### プロパティ44: メッセージコピーボタンのホバー表示

*任意の*メッセージに対して、ユーザーがホバーした際、システムはコピーボタンの不透明度を0から0.8に変更して表示する必要があります。

**検証: 要件 18.2**

### プロパティ45: メッセージコピー機能

*任意の*コピーボタンがクリックされた際、システムはメッセージのテキストコンテンツ（HTMLタグを除く）をクリップボードにコピーする必要があります。

**検証: 要件 18.3**

### プロパティ46: コピー成功時のフィードバック

*任意の*コピー操作が成功した際、システムはボタンのアイコンをコピーアイコンからチェックマークに変更し、2秒後に元のコピーアイコンに戻す必要があります。

**検証: 要件 18.4, 18.5**

### プロパティ47: コピーエラーハンドリング

*任意の*コピー操作が失敗した際、システムはエラーをコンソールに記録する必要があります。

**検証: 要件 18.6**

### プロパティ48: ページ読み込み時の設定復元

*任意の*ページ読み込み時に、システムはlocalStorageから保存されたサーバーアドレスとモデルを読み込み、UI要素に反映する必要があります。

**検証: 要件 19.1, 19.2**

### プロパティ49: 接続成功時の設定保存

*任意の*サーバー接続成功時に、システムはサーバーアドレスをlocalStorageに保存する必要があります。

**検証: 要件 19.3**

### プロパティ50: モデル選択時の設定保存

*任意の*モデル選択変更時に、システムは選択されたモデルをlocalStorageに保存する必要があります。

**検証: 要件 19.4**

### プロパティ51: 保存されたモデルの自動選択

*任意の*サーバー接続成功時に、保存されたモデルがモデル一覧に存在する場合、システムはそのモデルを自動的に選択する必要があります。

**検証: 要件 19.5**

### プロパティ52: デフォルトモデルの選択

*任意の*サーバー接続成功時に、保存されたモデルがモデル一覧に存在しない場合、システムはデフォルトのモデル（一覧の最初のモデル）を選択する必要があります。

**検証: 要件 19.6**

## コンポーネント: メッセージコピーボタン

### 責務
各メッセージにコピーボタンを追加し、メッセージ内容をクリップボードにコピーする機能を提供します。

### 実装詳細

**DOM構造**:
```html
<div class="message">
  <!-- メッセージヘッダー、コンテンツなど -->
  <button class="copy-message-btn" title="Copy message">
    <i class="fas fa-copy"></i>
  </button>
</div>
```

**CSSスタイル**:
```css
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
```

**JavaScript実装**:
```javascript
// addMessage関数内でコピーボタンを追加
const copyMessageBtn = document.createElement('button');
copyMessageBtn.className = 'copy-message-btn';
copyMessageBtn.innerHTML = '<i class="fas fa-copy"></i>';
copyMessageBtn.title = 'Copy message';
copyMessageBtn.addEventListener('click', () => {
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
```

### 主要機能

1. **ボタン表示制御**
   - デフォルトで非表示（opacity: 0）
   - メッセージホバー時に表示（opacity: 0.8）
   - ボタンホバー時に完全表示（opacity: 1）

2. **コピー機能**
   - Clipboard APIを使用してテキストをコピー
   - HTMLタグを除いたプレーンテキストをコピー
   - 非同期処理でエラーハンドリング

3. **視覚的フィードバック**
   - コピー成功時にアイコンをチェックマークに変更
   - 2秒後に元のコピーアイコンに戻る
   - トランジション効果で滑らかな表示切り替え

### エラーハンドリング

**コピー失敗時**:
```javascript
.catch(err => {
  console.error('Failed to copy message: ', err);
});
```
- エラーをコンソールに記録
- ユーザーには視覚的なエラー表示なし（ブラウザのClipboard API制限による）

### ブラウザ互換性

- Clipboard APIはHTTPS環境またはlocalhostでのみ動作
- 主要なモダンブラウザ（Chrome、Firefox、Safari、Edge）でサポート
- 古いブラウザではClipboard APIが利用できない場合があるため、エラーハンドリングが重要

## コンポーネント: サーバー設定永続化

### 責務
サーバーアドレスと選択したモデルをブラウザのlocalStorageに保存し、ページリロード後も自動的に復元する機能を提供します。

### 実装詳細

**localStorage キー**:
- `lmstudio_server_url`: サーバーアドレスを保存
- `lmstudio_model`: 選択したモデルIDを保存

**主要関数**:

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

**呼び出しタイミング**:

1. **ページ読み込み時**:
```javascript
// ページ読み込み完了後に設定を復元
loadSettings();
```

2. **接続成功時**:
```javascript
async function connectToServer() {
  // ... 接続処理 ...
  if (接続成功) {
    // 設定を保存
    saveSettings();
  }
}
```

3. **モデル選択変更時**:
```javascript
modelSelect.addEventListener('change', async (e) => {
  // ... モデル変更処理 ...
  currentModel = newModel;
  
  // 設定を保存
  saveSettings();
});
```

4. **保存されたモデルの復元**:
```javascript
async function connectToServer() {
  // ... モデル一覧取得 ...
  
  // 保存されたモデルを復元
  const savedModel = localStorage.getItem('lmstudio_model');
  if (savedModel && Array.from(modelSelect.options).some(opt => opt.value === savedModel)) {
    modelSelect.value = savedModel;
    currentModel = savedModel;
  } else {
    currentModel = modelSelect.value;
  }
  
  // 設定を保存
  saveSettings();
}
```

### 主要機能

1. **自動復元**
   - ページ読み込み時にlocalStorageから設定を読み込む
   - サーバーURL入力フィールドに自動入力
   - 接続成功後、保存されたモデルを自動選択

2. **自動保存**
   - 接続成功時にサーバーアドレスを保存
   - モデル選択変更時にモデルIDを保存
   - 非同期処理で保存操作を実行

3. **エラーハンドリング**
   - 保存されたモデルがモデル一覧に存在しない場合、デフォルトモデルを選択
   - localStorageが利用できない環境でもエラーを発生させない

### データフロー

```
ページ読み込み
    ↓
loadSettings() 実行
    ↓
localStorage から読み込み
    ↓
UI要素に反映
    ↓
ユーザーが接続ボタンをクリック
    ↓
connectToServer() 実行
    ↓
接続成功
    ↓
saveSettings() 実行
    ↓
localStorage に保存
    ↓
保存されたモデルを復元
    ↓
モデル選択ドロップダウンに反映
```

### ブラウザ互換性

- localStorageは主要なモダンブラウザでサポート
- プライベートブラウジングモード（シークレットモード）では制限がある場合がある
- ブラウザのキャッシュクリア時に設定も削除される

### セキュリティ考慮事項

- localStorageはドメインごとに分離されている
- HTTPSでない環境でも動作するが、セキュリティ上の理由からHTTPSの使用を推奨
- 機密情報（パスワードなど）は保存しない（現在はサーバーアドレスとモデルIDのみ）
