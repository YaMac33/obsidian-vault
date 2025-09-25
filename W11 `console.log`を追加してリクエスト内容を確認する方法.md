[[W10 Netlify Functionsで`console.log(event.body)`の中身を確認する方法]]


ジャンル・カテゴリ：ハウツー

[[#はじめに]]  
[[#consolelogを追加する場所]]  
[[#修正後のコード例]]  
[[#ログの確認方法]]  
[[#まとめ]]  

## はじめに
Netlify Functionsで「クライアントから送られてきたデータが正しく届いているか」を確認するには、`console.log` を仕込むのが一番シンプルです。ここでは、提示された `upload.js` のコードに `console.log` を追加する具体的な方法を紹介します。

## console.logを追加する場所
- **最初にリクエストが来た直後**  
  → `event.body` が正しく届いているか確認できる  
- **パース後の変数確認**  
  → `filename` や `mimeType` が欠けていないかを確認できる  

## 修正後のコード例
以下のように `console.log` を追加すると、リクエストの流れを追いやすくなります。

```js
const { google } = require('googleapis');
const stream = require('stream');

const CORS = (origin) => ({
  'Access-Control-Allow-Origin': process.env.ALLOWED_ORIGIN || '*',
  'Access-Control-Allow-Methods': 'GET,POST,OPTIONS',
  'Access-Control-Allow-Headers': 'Content-Type'
});

exports.handler = async (event) => {
  // CORS プリフライト対応
  if (event.httpMethod === 'OPTIONS') {
    return { statusCode: 204, headers: CORS(event.headers.origin) };
  }
  if (event.httpMethod !== 'POST') {
    return { statusCode: 405, body: 'Method Not Allowed', headers: CORS(event.headers.origin) };
  }

  try {
    const origin = event.headers.origin;
    const headers = CORS(origin);

    // ★リクエストの生データを確認
    console.log("=== Raw event.body ===");
    console.log(event.body);

    const body = JSON.parse(event.body);

    // ★パース後の中身を確認
    console.log("=== Parsed body ===");
    console.log(body);

    const { filename, mimeType, contentBase64 } = body;

    if (!filename || !contentBase64) {
      console.log("Missing fields:", { filename, mimeType, contentBase64 });
      return { statusCode: 400, body: 'Bad Request: missing fields', headers };
    }

    // サービスアカウント認証
    const key = JSON.parse(process.env.GOOGLE_SERVICE_ACCOUNT_KEY);
    const jwtClient = new google.auth.JWT({
      email: key.client_email,
      key: key.private_key,
      scopes: ['https://www.googleapis.com/auth/drive.file']
    });
    await jwtClient.authorize();

    const drive = google.drive({ version: 'v3', auth: jwtClient });

    // base64 -> Buffer -> Readable stream
    const buffer = Buffer.from(contentBase64, 'base64');
    const bufferStream = new stream.PassThrough();
    bufferStream.end(buffer);

    const res = await drive.files.create({
      requestBody: {
        name: filename,
        parents: process.env.DRIVE_PARENT_FOLDER_ID ? [process.env.DRIVE_PARENT_FOLDER_ID] : undefined
      },
      media: {
        mimeType: mimeType || 'application/octet-stream',
        body: bufferStream
      },
      fields: 'id, name, mimeType'
    });

    console.log("=== Google Drive upload result ===");
    console.log(res.data);

    return {
      statusCode: 200,
      headers,
      body: JSON.stringify({ ok: true, fileId: res.data.id, name: res.data.name })
    };
  } catch (err) {
    console.error("=== ERROR ===");
    console.error(err);
    return { statusCode: 500, body: 'Internal Server Error: ' + (err.message || err.toString()) };
  }
};
```

### ログの確認方法
ローカルで確認  
- netlify devを実行
- フロントから関数を叩くと、ターミナルに console.log の内容が出力される    
本番環境で確認 
- Netlify管理画面 → Functions → 該当関数 → Logs から確認可
  
## まとめ
- console.log(event.body) を追加すれば、クライアントから正しくデータが届いているか確認できる
- JSONパース後の変数も console.log するとデバッグが効率的になる
- ログはローカル (netlify dev) または本番環境のNetlifyダッシュボードで確認可能

---

※この記事はChatGPTの回答を基に作成しています。

[[W12  netlify devで「bash netlify  command not found」と出たときの対処法]]