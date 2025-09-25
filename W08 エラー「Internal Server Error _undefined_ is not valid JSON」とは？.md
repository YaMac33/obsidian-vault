[[W07 Netlifyのデプロイログを読み解く — 「Site is live」でも Open production deploy が Not Found のときにやること]]


ジャンル・カテゴリ：ハウツー

[[#はじめに]]  
[[#エラーメッセージの意味]]  
[[#よくある原因]]  
[[#解決方法]]  
[[#まとめ]]  

## はじめに
Web開発をしていると、API通信やフォーム送信の際に「Internal Server Error: "undefined" is not valid JSON」というエラーが表示されることがあります。これは一見難しそうですが、原因を分解すると理解しやすくなります。

## エラーメッセージの意味
- **Internal Server Error**  
  サーバー側で処理に失敗していることを示す一般的なエラー  
- **"undefined" is not valid JSON**  
  サーバーが受け取ったデータが `"undefined"` という文字列だったが、これはJSONの形式として不正である、という意味

要するに、「サーバーが期待するJSONデータを送られていない」ということです。

## よくある原因

### クライアント側（フロントエンド）の問題
- `fetch` や `axios` でリクエストする際、`body` に `undefined` を渡してしまっている  
- `JSON.stringify()` する前にデータが存在せず、`undefined` になっている  
- `Content-Type: application/json` を設定しているのに、実際の送信データがJSON形式でない

### サーバー側の問題
- リクエストボディを正しくパースできていない  
- エラーハンドリング不足で `undefined` をそのまま返している  
- サーバー側の期待するフィールドが送られていないため、処理中に `undefined` が発生

## 解決方法

### クライアント側の確認
- 送信前に `console.log(data)` をして、正しいオブジェクトがあるか確認する  
- `fetch` の場合は必ず `JSON.stringify()` を使ってオブジェクトを文字列に変換する  
```js
await fetch("/api/upload", {
  method: "POST",
  headers: {
    "Content-Type": "application/json"
  },
  body: JSON.stringify({ name: "test" }) // undefinedでないことを確認
})
```
## サーバー側の確認
- req.body をログ出力して、正しくデータが届いているか確認する
- 必要なバリデーション（必須項目チェック）を追加する
- JSONパースエラー時のエラーハンドリングを行う
## まとめ
- エラー「Internal Server Error: “undefined” is not valid JSON」は、サーバーが不正なJSON（undefined）を受け取ったことを意味する
- クライアント側では undefined を送っていないか確認する
- サーバー側では受け取ったデータを正しくパースできているかチェックする
- ログ出力を活用すれば、原因の切り分けがしやすくなる

---


※この記事はChatGPTの回答を基に作成しています。