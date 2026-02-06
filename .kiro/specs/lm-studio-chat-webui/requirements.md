# 要件定義書

## はじめに

LM Studio Chat WebUIは、ローカルでホストされているLM Studio APIサーバーに接続し、AIモデルとチャットできるブラウザベースのWebアプリケーションです。単一のHTMLファイルで構成されたスタンドアロンアプリケーションとして、ユーザーフレンドリーなインターフェースを通じて、複数のAIモデルとの対話、チャット履歴の管理、画像を含むマルチモーダルな会話を実現します。

## 用語集

- **System**: LM Studio Chat WebUIアプリケーション全体
- **LM_Studio_Server**: ローカルでホストされているLM Studio APIサーバー
- **Chat_Session**: ユーザーとAIモデル間の会話セッション
- **Message**: チャット内の個別のメッセージ（ユーザーまたはアシスタントから）
- **Model**: LM Studioサーバーで利用可能なAIモデル
- **Vision_Model**: 画像入力をサポートするAIモデル
- **Streaming_Response**: サーバーからリアルタイムで送信されるレスポンス
- **Markdown**: テキストフォーマット用のマークアップ言語
- **LaTeX**: 数式表現用の組版システム
- **Sidebar**: チャットセッション一覧を表示するサイドパネル

## 要件

### 要件1: サーバー接続管理

**ユーザーストーリー:** ユーザーとして、LM Studioサーバーに接続および切断できるようにしたい。これにより、AIモデルとの対話を開始または終了できる。

#### 受入基準

1. WHEN ユーザーがサーバーアドレスを入力してConnectボタンをクリックする THEN THE System SHALL LM_Studio_Serverへの接続を試行する
2. WHEN 接続が成功する THEN THE System SHALL 接続ステータスを「Connected」と表示し、利用可能なモデル一覧を取得する
3. WHEN 接続が失敗する THEN THE System SHALL 接続ステータスを「Failed to connect」と表示し、エラーメッセージを表示する
4. WHEN ユーザーが接続中にDisconnectボタンをクリックする THEN THE System SHALL サーバーから切断し、入力フィールドを無効化する
5. WHILE 接続されていない THEN THE System SHALL メッセージ入力フィールドと送信ボタンを無効化する

### 要件2: モデル選択と管理

**ユーザーストーリー:** ユーザーとして、利用可能なAIモデルから選択できるようにしたい。これにより、異なるモデルの特性を活用できる。

#### 受入基準

1. WHEN サーバーに接続する THEN THE System SHALL GET /v1/models エンドポイントを呼び出して利用可能なモデル一覧を取得する
2. WHEN モデル一覧を取得する THEN THE System SHALL ドロップダウンメニューにすべての利用可能なモデルを表示する
3. WHEN ユーザーが新しいモデルを選択する THEN THE System SHALL 現在ロードされているモデルをアンロードし、新しいモデルを設定する
4. WHEN モデルをアンロードする THEN THE System SHALL POST /v1/model/eject エンドポイントを呼び出す
5. WHILE モデル切り替え中 THEN THE System SHALL モデル選択ドロップダウンを一時的に無効化する

### 要件3: チャットセッション管理

**ユーザーストーリー:** ユーザーとして、複数のチャットセッションを作成、切り替え、削除できるようにしたい。これにより、異なるトピックの会話を整理できる。

#### 受入基準

1. WHEN ユーザーが「New Chat」ボタンをクリックする THEN THE System SHALL 新しい空のChat_Sessionを作成し、それをアクティブにする
2. WHEN 新しいChat_Sessionが作成される THEN THE System SHALL サイドバーのチャット一覧に追加し、デフォルト名「Conversation N」を割り当てる
3. WHEN ユーザーがサイドバーのチャットをクリックする THEN THE System SHALL そのChat_Sessionをアクティブにし、メッセージ履歴を表示する
4. WHEN ユーザーがチャット項目を右クリックする THEN THE System SHALL コンテキストメニューを表示し、削除オプションを提供する
5. WHEN ユーザーがチャットを削除する THEN THE System SHALL そのChat_Sessionをリストから削除し、別のチャットをアクティブにする
6. WHEN アシスタントが最初のメッセージを送信する THEN THE System SHALL Chat_Sessionの名前を会話内容の最初の7単語に基づいて更新する
7. WHEN すべてのチャットが削除される THEN THE System SHALL 自動的に新しいChat_Sessionを作成する

### 要件4: メッセージ送受信

**ユーザーストーリー:** ユーザーとして、テキストメッセージを送信し、AIモデルからのレスポンスを受信できるようにしたい。これにより、AIと対話できる。

#### 受入基準

1. WHEN ユーザーがメッセージを入力してEnterキーを押すまたは送信ボタンをクリックする THEN THE System SHALL メッセージをチャットコンテナに表示し、サーバーに送信する
2. WHEN メッセージを送信する THEN THE System SHALL POST /v1/chat/completions エンドポイントを呼び出し、会話履歴全体を含める
3. WHEN サーバーがレスポンスをストリーミングする THEN THE System SHALL リアルタイムでアシスタントメッセージを更新表示する
4. WHEN ストリーミングレスポンスが完了する THEN THE System SHALL メッセージをChat_Sessionの履歴に保存する
5. WHEN 空のメッセージを送信しようとする THEN THE System SHALL 送信を防止する
6. WHILE メッセージ送信中 THEN THE System SHALL 入力フィールドと送信ボタンを一時的に無効化する
7. WHEN レスポンス受信中にエラーが発生する THEN THE System SHALL エラーメッセージを表示し、接続ステータスを更新する

### 要件5: 会話履歴の構築

**ユーザーストーリー:** システムアーキテクトとして、会話コンテキストを正確に維持したい。これにより、AIが過去のメッセージを考慮した適切なレスポンスを生成できる。

#### 受入基準

1. WHEN メッセージを送信する THEN THE System SHALL システムプロンプトで始まる会話履歴を構築する
2. WHEN 画像メッセージが含まれる THEN THE System SHALL システムプロンプトを「You are an AI assistant that analyzes images.」に設定する
3. WHEN テキストのみのメッセージの場合 THEN THE System SHALL システムプロンプトを「You are an intelligent assistant. You always provide well-reasoned answers that are both correct and helpful.」に設定する
4. WHEN 会話履歴を構築する THEN THE System SHALL すべてのメッセージを送信順序で含め、各メッセージに適切なロール（user/assistant）を割り当てる
5. WHEN 画像メッセージを履歴に追加する THEN THE System SHALL コンテンツ配列にテキストとimage_urlオブジェクトの両方を含める

### 要件6: ビジョンモデル対応（画像アップロード）

**ユーザーストーリー:** ユーザーとして、画像をアップロードしてAIに分析してもらいたい。これにより、視覚的なコンテンツについて質問できる。

#### 受入基準

1. WHEN ユーザーが画像アップロードボタンをクリックする THEN THE System SHALL ファイル選択ダイアログを開く
2. WHEN ユーザーが画像ファイルを選択する THEN THE System SHALL 画像をBase64データURLとして読み込み、プレビューを表示する
3. WHEN 画像がプレビュー表示されている状態でメッセージを送信する THEN THE System SHALL 画像とテキストプロンプトの両方を含むメッセージを作成する
4. WHEN 画像のみでテキストがない場合 THEN THE System SHALL デフォルトプロンプト「What's in this image?」を使用する
5. WHEN 画像メッセージが送信される THEN THE System SHALL プレビューをクリアし、画像データをメッセージ履歴に保存する
6. WHEN 画像メッセージを履歴から読み込む THEN THE System SHALL 画像を適切に表示する

### 要件7: Markdownレンダリング

**ユーザーストーリー:** ユーザーとして、AIのレスポンスがMarkdown形式で適切にフォーマットされて表示されるようにしたい。これにより、構造化されたコンテンツを読みやすくなる。

#### 受入基準

1. WHEN アシスタントメッセージを表示する THEN THE System SHALL Marked.jsライブラリを使用してMarkdownをHTMLに変換する
2. WHEN Markdownに見出し（h1-h6）が含まれる THEN THE System SHALL 適切なフォントウェイトとマージンでレンダリングする
3. WHEN Markdownに段落が含まれる THEN THE System SHALL 適切な行間隔でレンダリングする
4. WHEN Markdownに引用ブロックが含まれる THEN THE System SHALL 左ボーダーと背景色を付けてレンダリングする
5. WHEN Markdownにインラインコードが含まれる THEN THE System SHALL 背景色とモノスペースフォントでレンダリングする
6. THE System SHALL GitHub Flavored Markdown（GFM）をサポートする
7. THE System SHALL 改行を自動的に<br>タグに変換する

### 要件8: コードブロックのシンタックスハイライト

**ユーザーストーリー:** ユーザーとして、コードブロックが言語に応じて適切にハイライトされて表示されるようにしたい。これにより、コードが読みやすくなる。

#### 受入基準

1. WHEN Markdownにコードブロックが含まれる THEN THE System SHALL Highlight.jsライブラリを使用してシンタックスハイライトを適用する
2. WHEN コードブロックに言語指定がある THEN THE System SHALL その言語の構文規則に従ってハイライトする
3. WHEN コードブロックが表示される THEN THE System SHALL Atom One Darkテーマを使用する
4. WHEN コードブロックがレンダリングされる THEN THE System SHALL 「Copy」ボタンを右上に追加する
5. WHEN ユーザーがCopyボタンをクリックする THEN THE System SHALL コードをクリップボードにコピーし、ボタンテキストを一時的に「Copied!」に変更する
6. WHEN コードのコピーが完了する THEN THE System SHALL 2秒後にボタンテキストを「Copy」に戻す

### 要件9: LaTeX数式レンダリング

**ユーザーストーリー:** ユーザーとして、数学的な表現がLaTeX形式で適切にレンダリングされるようにしたい。これにより、数式を美しく表示できる。

#### 受入基準

1. WHEN メッセージにLaTeX数式が含まれる THEN THE System SHALL MathJaxライブラリを使用してレンダリングする
2. THE System SHALL インライン数式として$...$と\\(...\\)の両方をサポートする
3. THE System SHALL ディスプレイ数式として$$...$$と\\[...\\]の両方をサポートする
4. THE System SHALL エスケープシーケンスを処理する
5. WHEN 数式のレンダリングが失敗する THEN THE System SHALL エラーをコンソールに記録し、元のテキストを表示する
6. THE System SHALL script、noscript、style、textarea、preタグ内のLaTeXを無視する

### 要件10: ストリーミングレスポンス処理

**ユーザーストーリー:** ユーザーとして、AIのレスポンスがリアルタイムで表示されるようにしたい。これにより、長いレスポンスでも待ち時間が短く感じられる。

#### 受入基準

1. WHEN サーバーにリクエストを送信する THEN THE System SHALL stream: trueパラメータを含める
2. WHEN ストリーミングレスポンスを受信する THEN THE System SHALL ReadableStreamリーダーを使用してチャンクを読み取る
3. WHEN 各チャンクを受信する THEN THE System SHALL "data:"で始まる行を解析する
4. WHEN チャンクにdelta.contentが含まれる THEN THE System SHALL 累積テキストに追加し、メッセージ表示を更新する
5. WHEN "[DONE]"シグナルを受信する THEN THE System SHALL ストリーミングを終了し、最終メッセージを保存する
6. WHEN ストリーミング中にエラーイベントを受信する THEN THE System SHALL ストリーミングを停止し、エラーメッセージを表示する
7. WHEN 各チャンク更新時 THEN THE System SHALL Markdownレンダリング、シンタックスハイライト、数式レンダリングを再適用する

### 要件11: サイドバーの折りたたみ機能

**ユーザーストーリー:** ユーザーとして、サイドバーを折りたたんでチャット領域を広げられるようにしたい。これにより、画面スペースを効率的に使用できる。

#### 受入基準

1. WHEN ユーザーがトグルボタンをクリックする THEN THE System SHALL サイドバーの幅を250pxから40pxに変更する
2. WHEN サイドバーが折りたたまれる THEN THE System SHALL サイドバーコンテンツの不透明度を0にし、ポインターイベントを無効化する
3. WHEN サイドバーが展開される THEN THE System SHALL サイドバーコンテンツを表示し、ポインターイベントを有効化する
4. THE System SHALL サイドバーの状態変更に0.3秒のトランジションアニメーションを適用する

### 要件12: レスポンシブデザイン

**ユーザーストーリー:** ユーザーとして、モバイルデバイスでもアプリケーションを快適に使用できるようにしたい。これにより、どのデバイスからでもアクセスできる。

#### 受入基準

1. WHEN 画面幅が480px以下の場合 THEN THE System SHALL サイドバーを非表示にする
2. WHEN モバイルビューの場合 THEN THE System SHALL メッセージの最大幅を90%に設定する
3. WHEN モバイルビューの場合 THEN THE System SHALL サーバーURL入力、モデル選択、接続ボタンを縦に配置する
4. THE System SHALL ビューポートメタタグでuser-scalable=noを設定し、ピンチズームを防止する
5. THE System SHALL すべてのインタラクティブ要素にタッチフレンドリーなサイズ（最小44x44px）を使用する

### 要件13: UIテーマとスタイリング

**ユーザーストーリー:** ユーザーとして、視覚的に魅力的で一貫性のあるダークモードUIを使用したい。これにより、長時間の使用でも目が疲れにくい。

#### 受入基準

1. THE System SHALL 紫をベースとしたカラースキームを使用する
2. THE System SHALL 背景色として#1e0a3cを使用する
3. THE System SHALL ユーザーメッセージに#6c2d8c、アシスタントメッセージに#442a6fを使用する
4. THE System SHALL ボタンとアクセント要素に#8e44adと#bb86fcを使用する
5. THE System SHALL すべてのインタラクティブ要素に0.3秒のホバートランジションを適用する
6. THE System SHALL 新しいメッセージにフェードインアニメーション（0.3秒）を適用する
7. THE System SHALL Google FontsのInterフォントファミリーを使用する
8. THE System SHALL すべてのカードとボタンに8pxのborder-radiusを適用する

### 要件14: メッセージメトリクス表示

**ユーザーストーリー:** ユーザーとして、レスポンス時間などのメトリクスを確認できるようにしたい。これにより、パフォーマンスを把握できる。

#### 受入基準

1. WHEN メッセージ送信を開始する THEN THE System SHALL performance.now()を使用して開始時刻を記録する
2. WHEN ストリーミングレスポンスが完了する THEN THE System SHALL 終了時刻を記録し、経過時間を計算する
3. WHEN 経過時間を計算する THEN THE System SHALL 秒単位で小数点以下2桁まで表示する
4. WHEN メトリクスが利用可能な場合 THEN THE System SHALL メッセージの下部右寄せで表示する

### 要件15: エラーハンドリング

**ユーザーストーリー:** ユーザーとして、エラーが発生した際に明確なフィードバックを受け取りたい。これにより、問題を理解し対処できる。

#### 受入基準

1. WHEN サーバー接続が失敗する THEN THE System SHALL 「Failed to connect」ステータスとエラーメッセージを表示する
2. WHEN メッセージ送信中にエラーが発生する THEN THE System SHALL 「Error: Unable to get a response from the server」メッセージを表示する
3. WHEN ストリーミング中にエラーイベントを受信する THEN THE System SHALL エラーをコンソールに記録し、ユーザーにエラーメッセージを表示する
4. WHEN コードのコピーが失敗する THEN THE System SHALL エラーをコンソールに記録する
5. WHEN MathJaxのレンダリングが失敗する THEN THE System SHALL エラーをコンソールに記録し、元のテキストを表示する
6. WHEN モデルのアンロードが失敗する THEN THE System SHALL エラーをコンソールに記録し、処理を続行する

### 要件16: データ永続化

**ユーザーストーリー:** システムアーキテクトとして、チャット履歴がセッション中メモリに保持されることを確認したい。これにより、ページをリロードするまで会話が維持される。

#### 受入基準

1. WHEN 新しいChat_Sessionが作成される THEN THE System SHALL メモリ内の配列に保存する
2. WHEN メッセージが送受信される THEN THE System SHALL 現在のChat_Sessionのmessages配列に追加する
3. WHEN ユーザーがChat_Sessionを切り替える THEN THE System SHALL 保存されたメッセージ履歴から読み込む
4. WHEN ページがリロードされる THEN THE System SHALL すべてのチャット履歴をクリアする（永続化なし）
5. WHEN 接続メッセージが表示される THEN THE System SHALL それをチャット履歴に保存しない

### 要件17: アクセシビリティとユーザビリティ

**ユーザーストーリー:** ユーザーとして、直感的で使いやすいインターフェースを使用したい。これにより、学習コストなく効率的に操作できる。

#### 受入基準

1. WHEN ページが読み込まれる THEN THE System SHALL サーバーURL入力フィールドに自動的にフォーカスする
2. WHEN メッセージ送信が完了する THEN THE System SHALL 入力フィールドに自動的にフォーカスを戻す
3. WHEN 新しいメッセージが追加される THEN THE System SHALL チャットコンテナを自動的に最下部までスクロールする
4. WHEN ユーザーがEnterキーを押す THEN THE System SHALL メッセージを送信する
5. THE System SHALL すべてのボタンにFont Awesomeアイコンを使用して視覚的な手がかりを提供する
6. THE System SHALL ホバー時にボタンの色を変更してインタラクティブ性を示す
7. THE System SHALL プレースホルダーテキストを使用して入力フィールドの目的を明確にする

### 要件18: メッセージコピー機能

**ユーザーストーリー:** ユーザーとして、ユーザーメッセージとアシスタントメッセージをワンクリックでクリップボードにコピーできるようにしたい。これにより、メッセージ内容を他のアプリケーションで簡単に再利用できる。

#### 受入基準

1. WHEN メッセージが表示される THEN THE System SHALL 各メッセージにコピーボタンを追加する
2. WHEN ユーザーがメッセージにホバーする THEN THE System SHALL コピーボタンを表示する（不透明度0から0.8に変更）
3. WHEN ユーザーがコピーボタンをクリックする THEN THE System SHALL メッセージのテキストコンテンツ（HTMLタグを除く）をクリップボードにコピーする
4. WHEN コピーが成功する THEN THE System SHALL ボタンのアイコンをコピーアイコンからチェックマークに変更する
5. WHEN コピー成功後2秒経過する THEN THE System SHALL ボタンのアイコンを元のコピーアイコンに戻す
6. WHEN コピーが失敗する THEN THE System SHALL エラーをコンソールに記録する
7. THE System SHALL コピーボタンをメッセージの右上に配置する
8. THE System SHALL コピーボタンにツールチップ「Copy message」を表示する

### 要件19: サーバー設定の永続化

**ユーザーストーリー:** ユーザーとして、一度入力したサーバーアドレスと選択したモデルがブラウザに保存され、ページをリロードしても復元されるようにしたい。これにより、毎回サーバー情報を再入力する手間を省ける。

#### 受入基準

1. WHEN ページが読み込まれる THEN THE System SHALL localStorageから保存されたサーバーアドレスとモデルを読み込む
2. WHEN 保存されたサーバーアドレスが存在する THEN THE System SHALL サーバーURL入力フィールドに自動的に入力する
3. WHEN サーバーへの接続が成功する THEN THE System SHALL サーバーアドレスをlocalStorageに保存する
4. WHEN モデル選択が変更される THEN THE System SHALL 選択されたモデルをlocalStorageに保存する
5. WHEN サーバーへの接続が成功し、保存されたモデルがモデル一覧に存在する THEN THE System SHALL そのモデルを自動的に選択する
6. WHEN 保存されたモデルがモデル一覧に存在しない THEN THE System SHALL デフォルトのモデル（一覧の最初のモデル）を選択する
7. THE System SHALL localStorageのキー名として'lmstudio_server_url'と'lmstudio_model'を使用する
