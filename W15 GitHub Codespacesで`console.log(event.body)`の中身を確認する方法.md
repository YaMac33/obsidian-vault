
[[W14 GoogleドライブAPIのサービスアカウントJSONで「＼n」が既に入っているかの確認方法]]

ジャンル・カテゴリ：ハウツー

[[#はじめに]]
[[#確認のポイント]]
[[#手順1 Netlify CLIのインストール]]
[[#手順2 ローカルサーバーの起動]]
[[#手順3 テスト用リクエストの送信]]
[[#まとめ]]

## はじめに
Netlify Functions内で `console.log(event.body)` を書いた場合、その出力結果は **サーバーログ** に表示されます。  
GitHub Codespacesを利用する場合も同様で、開発環境でNetlify Functionsを実行し、コンソール出力を確認する流れになります。

## 確認のポイント
- `console.log` の出力は **ブラウザの画面には表示されない**  
- **Netlify CLI (`netlify dev`) を使って関数をローカル実行** → **ターミナルに出力が表示される**

## 手順1 Netlify CLIのインストール
Codespacesでターミナルを開き、以下を実行します。

```bash
npm install -g netlify-cli
```

これで `netlify` コマンドが使えるようになります。

## 手順2 ローカルサーバーの起動
Codespaces内で以下を実行します。

```bash
netlify dev
```

- デフォルトで `http://localhost:8888` が起動  
- Netlify Functionsは `.netlify/functions/関数名` の形でアクセス可能  
- この状態で、`console.log(event.body)` の出力はターミナルに表示されます  

## 手順3 テスト用リクエストの送信
関数を呼び出すには、以下の方法があります。

- **ブラウザからアップロードフォームを利用**
  - 実際のフォームからファイルを選択 → 送信すると、サーバーログに `event.body` が表示される  

- **cURLコマンドを使う**
  ```bash
  curl -X POST http://localhost:8888/.netlify/functions/upload \
    -H "Content-Type: application/json" \
    -d '{"filename":"test.txt","mimeType":"text/plain","contentBase64":"ZGVtbw=="}'
  ```

送信後、Codespacesのターミナルに `console.log(event.body)` の内容が出力されます。
 

## Netlify Functions 内で `console.log(event.body)` を書く方法

## 結論
Netlify Functions は Node.js 上で動作するため、通常の JavaScript と同じように `console.log()` を使えます。`event.body` をログに出したい場合は、以下のようにコードを記述すればOKです。

## サンプルコード

```javascript
// netlify/functions/upload.js
exports.handler = async (event, context) => {
  // 受け取ったリクエストの body をログに出力
  console.log("イベントボディ:", event.body);

  return {
    statusCode: 200,
    body: JSON.stringify({ message: "アップロード成功！" }),
  };
};
```
  
## ポイント
- event.body は 文字列として受け取られる ことが多いです。  
- JSON データを送信している場合は JSON.parse(event.body) でオブジェクトに変換できます。
- ログの出力先は Netlify の Function Logs です。デプロイ後、Netlify の管理画面から確認できます。

## JSON データを扱う例
```
exports.handler = async (event, context) => {
  console.log("生の body:", event.body);

  // JSON を送っている場合
  const data = JSON.parse(event.body);
  console.log("パース後のデータ:", data);

  return {
    statusCode: 200,
    body: JSON.stringify({ received: data }),
  };
};
```

## Netlify の Function Logs を確認する方法

## 結論
Netlify Functions の `console.log()` 出力は、**Netlify の管理画面の「Function Logs」** から確認できます。ローカルで動かす場合は、ターミナル上に表示されます。

---
## 確認手順

### 1. デプロイ後（本番環境）
1. Netlify にログイン
2. 対象サイトを選択
3. 左側メニューから **「Functions」** をクリック
4. 関数名を選択すると、**ログ一覧** が表示されます

👉 `console.log()` の内容は、このログに記録されています。  
👉 エラー時の `console.error()` も同様にここで確認できます。

---
### 2. ローカル開発環境（`netlify dev` 使用時）
```bash
netlify dev
```
を実行すると、ターミナル上に以下のような出力が表示されます。

```
◈ Starting Netlify Dev with functions...
◈ Functions server is listening on 34567

イベントボディ: {"name":"Taro","age":20}
```
つまり、console.log(event.body) の内容は リアルタイムでターミナルに出力 されます。
## ポイント
- 本番環境：Netlify のダッシュボード > Functions > ログ
- ローカル環境：ターミナル（netlify dev 実行中）
## まとめ
- **`npm install -g netlify-cli` と `netlify dev` は必須**  
- Codespacesで `console.log` の内容を確認するには、Netlify CLIを使ってローカル実行する  
- 出力は **ブラウザではなくターミナル** に表示される  
- テストはフォーム送信または `cURL` を使って行える 
- console.log(event.body) はそのまま書ける。
- body が JSON なら JSON.parse() して使う。
- 出力結果は Netlify のログで確認可能。
- console.log() の結果は ローカルならターミナル、本番なら Netlify ダッシュボード で確認できる。
- デバッグ時はまず netlify dev を活用するのがおすすめ。

※この記事はChatGPTの回答を基に作成しています。