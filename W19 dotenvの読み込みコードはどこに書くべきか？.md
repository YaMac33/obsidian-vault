[[W18 APIキーのJSONファイルを.envファイルに保存し、.gitignoreで除外する方法]]


ジャンル・カテゴリ：ハウツー

[[#はじめに]]
[[#dotenvの役割]]
[[#どのファイルに書くべきか]]
[[#script.jsに書く場合]]
[[#upload.jsに書く場合]]
[[#ベストプラクティス]]
[[#まとめ]]

## はじめに
環境変数を利用する際には、`dotenv`を使って`.env`ファイルを読み込みます。この記事では、以下のコードをどのファイルに記述すべきかを解説します。
```javascript
require('dotenv').config();

console.log(process.env.PROJECT_ID);
console.log(process.env.CLIENT_EMAIL);
```

## dotenvの役割
- `.env`ファイルを読み込んで環境変数としてアプリに渡す  
- APIキーや認証情報をコードに直書きしないための仕組み  
- Node.jsアプリ起動時に一度だけ読み込めばOK  

## どのファイルに書くべきか
基本ルールとしてはアプリのエントリーポイント（最初に実行されるファイル）に記述するのがベストです。  
そうすることで、後続のファイルすべてから環境変数を利用できます。  

### script.jsに書く場合
- script.jsがブラウザ側で動作する場合は**NG**  
  → ブラウザで`.env`の値を直接参照することはできません。  
- Node.jsの実行スクリプトとして使っている場合は、冒頭に記述すれば問題ありません。  

### upload.jsに書く場合
- upload.jsがAPIサーバー側の処理や、Netlify Functionsなどのバックエンドで動く場合は有効です。  
- ただし、プロジェクト全体で使うのであれば、upload.jsだけでなく他のファイルからも参照する可能性があります。  

## ベストプラクティス
- エントリーポイント（例：`index.js`や`server.js`）に記述する  
- script.jsやupload.jsはモジュールとして利用し、環境変数はそれらの中で直接読み込むのではなく、すでに読み込まれた`process.env`を参照する形にする  
- 必要に応じて以下のような`config.js`を作って共通化するのもおすすめです  

```js
// config.js
require('dotenv').config();

module.exports = {
  projectId: process.env.PROJECT_ID,
  clientEmail: process.env.CLIENT_EMAIL
};
```

```js
// upload.js
const { projectId, clientEmail } = require('./config');
console.log(projectId, clientEmail);
```

## まとめ
- `require('dotenv').config()`は基本的にアプリの最初に実行されるファイルに書く  
- script.jsがフロント側ならNG、サーバー側ならOK  
- upload.jsに直接書くより、共通の`config.js`でまとめるのがおすすめ  

※この記事はChatGPTの回答を基に作成しています。

