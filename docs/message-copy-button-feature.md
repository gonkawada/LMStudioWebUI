# メッセージコピーボタン機能

## 概要

ユーザーメッセージとアシスタントメッセージの両方に、ワンクリックでメッセージ内容をクリップボードにコピーできるボタンを追加しました。

## 実装内容

### 1. CSSスタイルの追加

#### メッセージカードの位置設定
```css
.message {
  position: relative;  /* コピーボタンを絶対位置で配置するため */
}
```

#### コピーボタンのスタイル
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
  opacity: 0;                    /* デフォルトで非表示 */
  transition: opacity 0.2s;
  z-index: 5;
}

.message:hover .copy-message-btn { 
  opacity: 0.8;                  /* ホバー時に表示 */
}

.copy-message-btn:hover { 
  opacity: 1 !important;         /* ボタン自体のホバー時は完全に表示 */
}
```

### 2. JavaScript機能の追加

`addMessage`関数内に以下の機能を追加:

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
```

## 機能の特徴

1. **ホバー時表示**: メッセージにマウスをホバーすると、右上にコピーボタンが表示されます
2. **ワンクリックコピー**: ボタンをクリックすると、メッセージのテキスト内容がクリップボードにコピーされます
3. **視覚的フィードバック**: コピー成功時、アイコンがコピーマーク（📋）からチェックマーク（✓）に2秒間変わります
4. **HTMLタグ除外**: コピーされるのはプレーンテキストのみで、HTMLタグは含まれません
5. **すべてのメッセージに対応**: ユーザーメッセージとアシスタントメッセージの両方にボタンが表示されます

## テスト手順

### 手動テスト

1. **基本機能のテスト**
   - [ ] index.htmlをブラウザで開く
   - [ ] LM Studioサーバーに接続
   - [ ] メッセージを送信
   - [ ] ユーザーメッセージにマウスをホバーし、コピーボタンが表示されることを確認
   - [ ] コピーボタンをクリック
   - [ ] テキストエディタに貼り付けて、メッセージ内容が正しくコピーされていることを確認

2. **アシスタントメッセージのテスト**
   - [ ] アシスタントからの返信を受信
   - [ ] アシスタントメッセージにマウスをホバーし、コピーボタンが表示されることを確認
   - [ ] コピーボタンをクリック
   - [ ] テキストエディタに貼り付けて、メッセージ内容が正しくコピーされていることを確認

3. **視覚的フィードバックのテスト**
   - [ ] コピーボタンをクリック
   - [ ] アイコンがチェックマークに変わることを確認
   - [ ] 2秒後にコピーアイコンに戻ることを確認

4. **Markdown/コードブロックのテスト**
   - [ ] Markdownフォーマットを含むメッセージを送信（見出し、リスト、コードブロックなど）
   - [ ] コピーボタンでコピー
   - [ ] プレーンテキストとして正しくコピーされることを確認（HTMLタグが含まれていないこと）

5. **画像メッセージのテスト**
   - [ ] 画像を含むメッセージを送信
   - [ ] コピーボタンでコピー
   - [ ] テキスト部分のみがコピーされることを確認

6. **レスポンシブデザインのテスト**
   - [ ] ブラウザウィンドウのサイズを変更
   - [ ] モバイルビュー（480px以下）でもコピーボタンが正しく表示されることを確認

7. **複数メッセージのテスト**
   - [ ] 複数のメッセージを送受信
   - [ ] 各メッセージのコピーボタンが独立して動作することを確認

## ブラウザ互換性

- **Chrome/Edge**: ✓ 完全サポート
- **Firefox**: ✓ 完全サポート
- **Safari**: ✓ 完全サポート（Clipboard API対応）
- **モバイルブラウザ**: ✓ サポート（タッチ操作でホバー状態を維持）

## 既知の制限事項

1. **Clipboard API**: 古いブラウザではClipboard APIがサポートされていない場合があります
2. **HTTPS要件**: 一部のブラウザでは、HTTPSでない場合にClipboard APIが制限される場合があります（ローカルファイルは問題なし）

## 今後の改善案

1. フォールバック機能の追加（Clipboard APIが利用できない場合）
2. コピー形式の選択（プレーンテキスト/Markdown/HTML）
3. キーボードショートカットの追加（Ctrl+C でメッセージをコピー）
4. コピー履歴機能

## 関連ファイル

- `index.html`: メイン実装ファイル
- `docs/index-html-code-explanation.md`: コード解説ドキュメント

## 変更履歴

- 2024-XX-XX: 初回実装（メッセージコピーボタン機能）
