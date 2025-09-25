[[W08 エラー「Internal Server Error "undefined" is not valid JSON」とは？]]


ジャンル・カテゴリ：ハウツー

[[#はじめに]]  
[[#今回の状況整理]]  
[[#エラーメッセージの意味]]  
[[#原因の可能性]]  
[[#確認すべきポイント]]  
[[#まとめ]]  

## はじめに
NetlifyにデプロイしたWebページで、Google Drive APIを利用しファイルアップロードを試みる際に「Internal Server Error: "undefined" is not valid JSON」というエラーが発生することがあります。ここでは、Google DriveのAPIキーやGitHubにpushした認証情報（JSONファイル）が原因なのか、それ以外の可能性があるのかを整理します。

## 今回の状況整理
- `script.js` 内で `FUNCTION_URL` を `.netlify/functions/upload` に変更 → 「loads fails」は解決  
- その後、**JSON関連のエラー**が発生  
- Google Drive APIを使うためにサービスアカウントJSONを取得済み  
- そのJSONをNetlifyに環境変数として利用している状態  

## エラーメッセージの意味
- **"undefined" is not valid JSON**  
  サーバーレス関数（Netlify Functions）が「undefined」という値をJSONとして処理しようとして失敗している  
- つまり「Googleの認証情報が壊れている」というよりは、**関数に渡すデータが正しくない**ケースが多い

## 原因の可能性

### 認証情報(JSON)が原因のケース
- サービスアカウントのJSONを誤って編集・破損した  
- 必要なフィールド（`client_email` や `private_key`）が欠けている  
- 環境変数に正しく設定せず、`undefined` が渡っている

### 関数のコードやリクエスト側が原因のケース
- `req.body` が `undefined` のまま `JSON.parse()` されている  
- クライアント側で `fetch` の `body` を渡し忘れている  
- `Content-Type: application/json` がヘッダーにないため、JSONとして認識されない  
- 認証情報は正しいが、**Netlifyに渡す処理の段階でデータが欠落**している

## 確認すべきポイント

### 1. GitHubにpushしたJSONの扱い方
- サービスアカウントのJSONは、**そのままGitHubにpushするのは危険**（セキュリティリスク大）  
- 本来はNetlifyの「環境変数」に設定し、`process.env` 経由で読み込むべき  

### 2. Netlify Functionsのコード確認
- `upload.js` などの関数で `JSON.parse(event.body)` を使っている場合、まず `console.log(event.body)` で中身を確認  
- `undefined` が来ているなら、フロントから正しく送られていない可能性大  

### 3. クライアント側のリクエスト確認
```js
await fetch(FUNCTION_URL, {
  method: "POST",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify({ filename: "test.txt", content: "hello" })
});
```
- このように必ずオブジェクトを JSON.stringify() して送信する
- body: undefined のまま送信すると今回のエラーが発生する

## まとめ
- エラーの直接の原因は「Google DriveのJSONそのものが壊れている」よりも、Netlify Functionsに渡すリクエストボディがundefinedになっている可能性が高い
- サービスアカウントJSONはGitHubにpushせず、Netlifyの環境変数に登録して読み込むのが正しい方法
- デバッグとしては、  
1. フロントから送るリクエストを console.log で確認
2. Netlify Functions側で event.body を console.log  
    → これで「undefined」になっている箇所を切り分けられる

---

※この記事はChatGPTの回答を基に作成しています。

[[W10 Netlify Functionsで`console.log(event.body)`の中身を確認する方法]]
