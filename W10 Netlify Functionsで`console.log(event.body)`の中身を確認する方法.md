[[W09 「Internal Server Error "undefined" is not valid JSON」の原因はどこにあるのか？]]

ジャンル・カテゴリ：ハウツー

[[#はじめに]]  
[[#eventbodyとは]]  
[[#consolelogの使い方]]  
[[#ログの確認方法]]  
[[#注意点]]  
[[#まとめ]]  

## はじめに
Netlify Functionsを使ってAPI処理を実装するとき、「クライアントから正しくデータが送信されているか」を確認するために `console.log(event.body)` を使うことがよくあります。しかし「どうやってログを見るのか？」と迷う人も多いはず。この記事では、その方法を整理します。

## event.bodyとは
- Netlify Functionsで関数を定義すると、第一引数に `event` が渡される  
- この `event.body` には、クライアント（ブラウザやアプリ）から送られてきたリクエストの内容が文字列として格納される  
- 例：`fetch` で `body: JSON.stringify({ name: "Taro" })` を送ると、`event.body` の中身は `"{"name":"Taro"}"` という文字列になる

## console.logの使い方
関数ファイル（例：`netlify/functions/upload.js`）で以下のように記述します。

```js
exports.handler = async (event, context) => {
  console.log("=== event.body ===");
  console.log(event.body);

  return {
    statusCode: 200,
    body: JSON.stringify({ message: "OK" })
  };
};
```

## ログの確認方法
### 1. ローカル開発環境で確認
Netlify CLIを使うと簡単に確認できます。
```
npm install -g netlify-cli
netlify dev
```

その状態でWebページから関数を呼び出すと、ターミナルに console.log(event.body) の内容が表示されます。

### 2. Netlifyにデプロイ後に確認
デプロイ済み環境で確認したい場合は、Netlifyの管理画面でログを確認できます。
- Netlify管理画面 → Site → Functions → 対象の関数 → Logs
- または Deploy logs でも直近の実行内容が見られる場合があります

## 注意点
- event.body は文字列なので、そのままではオブジェクトとして扱えません  
    → JSON.parse(event.body) を使うとオブジェクト化できます
- 機密情報（APIキーやトークン）を console.log するとログに残るので注意
- デバッグ目的で使ったログは不要になったら削除しましょう

## まとめ
- event.body にはクライアントから送られたリクエスト内容が文字列で入る
- console.log(event.body) を書けば、ターミナルやNetlify管理画面のログに内容が出力される
- ローカルでは netlify dev、本番では Functions → Logs から確認できる
- 必要に応じて JSON.parse() でオブジェクト化すると扱いやすい

---

※この記事はChatGPTの回答を基に作成しています。

[[W11 `console.log`を追加してリクエスト内容を確認する方法]]
