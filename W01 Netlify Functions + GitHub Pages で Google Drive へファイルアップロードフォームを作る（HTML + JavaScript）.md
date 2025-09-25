

**ジャンル（カテゴリ）**：ハウツー（How-to）

## 概要
このガイドは、静的サイト（GitHub Pages）上のHTML＋JavaScriptから、Netlify Functions（サーバーレス）を経由して Google Drive にファイルをアップロードする仕組みを、必要なファイル構成・サンプルコード・Netlifyへのデプロイ手順までまとめた「実践チュートリアル」です。用途は「ユーザーがフォームでファイルを送信し、管理者側の Google Drive フォルダに保存する」想定です。

> 重要ポイント（先に知っておくべきこと）
- Netlify Functions の同期関数やペイロードにはサイズ制限があります。小〜中サイズ（数MB）までのアップロードを想定するか、**resumable upload（分割/再開アップロード）** を採用してください。  [oai_citation:0‡Netlify Docs](https://docs.netlify.com/build/functions/overview/?utm_source=chatgpt.com)  
- Netlify Functions は Node.js ランタイムで動作します（最近は Node 18 がデフォルト）。関数の実装は Node.js（JavaScript）で作るのが手軽です。  [oai_citation:1‡Netlify Docs](https://docs.netlify.com/build/functions/get-started/?utm_source=chatgpt.com)

---

## 推奨アーキテクチャ（高レベル）
- フロント（静的）: GitHub Pages 上の HTML / JS（フォーム）。  
- バックエンド（API）: Netlify Functions（エンドポイント） — この関数が Google Drive API にアクセスしてファイルを作成する。  
- ストレージ: Google Drive（サービスアカウント or 専用アカウントのフォルダ）。  
  - サービスアカウントを作成して、アップロード先フォルダをサービスアカウントのメールに共有する運用が実用的です。  [oai_citation:2‡デジタルインスピレーション](https://www.labnol.org/google-api-service-account-220404?utm_source=chatgpt.com)

---

## 準備（概略）
- Google Cloud Console：プロジェクト作成 → Google Drive API を有効化 → サービスアカウント作成 → JSON キーをダウンロード。アップロード先フォルダをサービスアカウントに編集者として共有。  [oai_citation:3‡Google for Developers](https://developers.google.com/workspace/drive/api/guides/manage-uploads?utm_source=chatgpt.com)
- GitHub：静的サイトをリポジトリに置き、GitHub Pages を有効化（任意のブランチ / docs フォルダなど）。
- Netlify：アカウント作成。Netlify Functions を配置するためのリポジトリ（同一でも別リポジトリでも可）を用意。環境変数にサービスアカウント JSON（文字列化）とフォルダID等を設定。
  - Functions のデプロイ方法：Git連携（自動） or Netlify CLI（手動）など。  [oai_citation:4‡Netlify Docs](https://docs.netlify.com/build/functions/deploy/?utm_source=chatgpt.com)

---

## 推奨ファイル構成（例）
- repository-root/
  - docs/                       ← GitHub Pages 用の公開フォルダ（例）
    - index.html
    - script.js
    - style.css
  - netlify/
    - functions/
      - upload.js               ← Netlify Function（ファイルアップロード受信 → Driveへ保存）
  - netlify.toml
  - package.json
  - README.md

---

## 環境変数（Netlify に設定）
- `GOOGLE_SERVICE_ACCOUNT_KEY` : サービスアカウントの JSON を文字列として格納（改行含む場合は適切にエスケープ）。  
- `DRIVE_PARENT_FOLDER_ID` : アップロード先の Google Drive フォルダID。  
- `ALLOWED_ORIGIN` : フロント側（GitHub Pages）のオリジン（例: https://yourname.github.io）。  
（注意）**サービスアカウントの JSON をクライアント側に渡さない**こと。必ずサーバ側（Netlify の環境変数）で管理。

---

## サンプル：フロント（index.html + script.js）
### index.html（最小フォーム）
    <!doctype html>
    <html>
    <head>
      <meta charset="utf-8">
      <meta name="viewport" content="width=device-width,initial-scale=1">
      <title>ファイルアップロード</title>
    </head>
    <body>
      <h1>ファイルアップロード</h1>
      <form id="uploadForm">
        <input type="file" id="fileInput" name="file" required>
        <input type="text" id="caption" name="caption" placeholder="説明（任意）">
        <button type="submit">アップロード</button>
      </form>
      <pre id="result"></pre>
      <script src="./script.js"></script>
    </body>
    </html>

### script.js（クライアント側ロジック）
    const FUNCTION_URL = 'https://YOUR-NETLIFY-SITE.netlify.app/.netlify/functions/upload'; // <-- Netlify の関数URLに置き換える

    function fileToDataURL(file) {
      return new Promise((resolve, reject) => {
        const reader = new FileReader();
        reader.onload = () => resolve(reader.result);
        reader.onerror = reject;
        reader.readAsDataURL(file);
      });
    }

    document.getElementById('uploadForm').addEventListener('submit', async (e) => {
      e.preventDefault();
      const file = document.getElementById('fileInput').files[0];
      if (!file) return alert('ファイルを選んでください');
      // サイズチェック（クライアント側で制限）
      const MAX_BYTES = 4 * 1024 * 1024; // 例: 4MB（Netlify のペイロード制限を考慮）
      if (file.size > MAX_BYTES) {
        return alert('ファイルが大きすぎます（最大 4MB）');
      }
      try {
        const dataUrl = await fileToDataURL(file);
        const base64 = dataUrl.split(',')[1]; // "data:<mime>;base64,<data>" の後半
        const payload = {
          filename: file.name,
          mimeType: file.type || 'application/octet-stream',
          contentBase64: base64,
          caption: document.getElementById('caption').value || ''
        };
        const res = await fetch(FUNCTION_URL, {
          method: 'POST',
          headers: { 'Content-Type': 'application/json' },
          body: JSON.stringify(payload)
        });
        const text = await res.text();
        if (!res.ok) throw new Error(text || 'Upload failed');
        document.getElementById('result').textContent = 'アップロード成功: ' + text;
      } catch (err) {
        document.getElementById('result').textContent = 'エラー: ' + err.message;
      }
    });

---

## サンプル：Netlify Function（upload.js）
**注意**: この関数は「POST で JSON（base64 のファイルデータ）を受け取り、Google Drive API にアップロードする」最小実装です。`GOOGLE_SERVICE_ACCOUNT_KEY` と `DRIVE_PARENT_FOLDER_ID` を Netlify 環境変数で設定してください。

```
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

        const body = JSON.parse(event.body);
        const { filename, mimeType, contentBase64 } = body;
        if (!filename || !contentBase64) {
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

        return {
          statusCode: 200,
          headers,
          body: JSON.stringify({ ok: true, fileId: res.data.id, name: res.data.name })
        };
      } catch (err) {
        console.error(err);
        return { statusCode: 500, body: 'Internal Server Error: ' + (err.message || err.toString()) };
      }
    };
```

**補足（現実運用上の注意）**
- Netlify の同期関数のリクエストサイズ上限・エンコードのオーバーヘッドを考慮し、Base64 を使う場合は実効上さらに小さなサイズ制限になります。小さいファイルを想定するか、別の手段（分割アップロード／resumable）を検討してください。  [oai_citation:5‡Netlify Docs](https://docs.netlify.com/build/functions/overview/?utm_source=chatgpt.com)
- 大きなファイル（数十MB 以上）を扱う場合は、Google Drive の **resumable upload** を使うか、Google Cloud Storage の署名付き URL を用いるワークフローを検討してください（Drive API は resumable をサポート）。  [oai_citation:6‡Google for Developers](https://developers.google.com/workspace/drive/api/guides/manage-uploads?utm_source=chatgpt.com)

---

## package.json（最小、Netlify 用）
    {
      "name": "drive-uploader-netlify",
      "version": "1.0.0",
      "dependencies": {
        "googleapis": "^118.0.0"
      }
    }

（Netlify にデプロイする前にローカルで `npm install` を行って依存を準備しておくと、CLI デプロイがスムーズです。）

---

## netlify.toml（例）
    [build]
      publish = "public"          # Netlify に公開する静的ファイル（functions-only サイトにする場合は空でも可）
      functions = "netlify/functions"

    [[headers]]
      for = "/*"
      [headers.values]
        Access-Control-Allow-Origin = "*"

（GitHub Pages で静的ファイルを公開する場合は `publish` を空にして functions のみデプロイするワークフローも可能です。Netlify 側で functions のみのサイトを作るか、Functions を含む別リポジトリを用意してデプロイする運用を検討してください。デプロイの詳細は Netlify のドキュメント参照。）  [oai_citation:7‡Netlify Docs](https://docs.netlify.com/build/functions/deploy/?utm_source=chatgpt.com)

---

## デプロイ手順（具体的）
### 1) Google 側の準備
- Google Cloud Console でプロジェクトを作成し、Google Drive API を有効化。 [oai_citation:8‡Google for Developers](https://developers.google.com/workspace/drive/api/guides/manage-uploads?utm_source=chatgpt.com)  
- サービスアカウントを作成 → キー（JSON）をダウンロード。  
- Google Drive 上でアップロード先のフォルダを作成し、そのフォルダをサービスアカウントのメールアドレス（`xxxxx@xxxx.iam.gserviceaccount.com`）に編集者として共有。 [oai_citation:9‡デジタルインスピレーション](https://www.labnol.org/google-api-service-account-220404?utm_source=chatgpt.com)

### 2) リポジトリ準備
- 上述のファイル構成でリポジトリを作る。`docs/` を GitHub Pages の公開フォルダにすると簡単。

### 3) Netlify 側
**選択肢 A（推奨：Netlify にサイト＋関数を置く）**  
- GitHub リポジトリを Netlify と連携して自動デプロイ。Netlify のサイト設定で `Functions directory` を `netlify/functions`、`Build & publish` を設定。デプロイで関数が自動的にビルド・公開されます。 [oai_citation:10‡Netlify Docs](https://docs.netlify.com/build/functions/deploy/?utm_source=chatgpt.com)

**選択肢 B（GitHub Pages を残しつつ Functions のみ Netlify に置く）**  
- 「関数のみ」を含む別リポジトリを作り、Netlify と連携して関数のみ公開する（Netlify 側で関数のみ公開可能）。もしくは Netlify CLI の `netlify deploy --dir=public --functions=netlify/functions` を利用して手動で関数をデプロイすることも可能。CLI を使う場合は事前に `npm install` して依存を準備しておく必要があります。 [oai_citation:11‡Netlify Docs](https://docs.netlify.com/api-and-cli-guides/cli-guides/get-started-with-cli/?utm_source=chatgpt.com)

### 4) 環境変数の設定（Netlify UI）
- `GOOGLE_SERVICE_ACCOUNT_KEY`（JSON を文字列化）、`DRIVE_PARENT_FOLDER_ID`、`ALLOWED_ORIGIN` を Netlify サイトの「Site settings → Build & deploy → Environment」へ登録。

### 5) 動作確認
- GitHub Pages 上のフォームからファイルを選び送信 → Netlify Function のログで受信・Drive への作成を確認。Netlify ダッシュボードで関数ログを確認できます。 [oai_citation:12‡Netlify Docs](https://docs.netlify.com/build/functions/deploy/?utm_source=chatgpt.com)

---

## 運用上の注意・ベストプラクティス
- **セキュリティ**：サービスアカウント JSON を絶対にクライアントへ流さない。Netlify の環境変数に保管する。  
- **CORS**：関数で `Access-Control-Allow-Origin` を設定し、必要なオリジンだけ通す（`*` は開発時のみ推奨）。  
- **サイズ制限**：Netlify の関数リクエストサイズ上限を超えないように（Base64 によるエンコードは更にサイズを増やす）か、resumable upload を検討。  [oai_citation:13‡Netlify Docs](https://docs.netlify.com/build/functions/overview/?utm_source=chatgpt.com)  
- **監査ログ・削除運用**：Drive に貯まったファイルの整理ルールを決める（名前付け規則、メタデータ、ロギング）。  
- **ウイルススキャン/検査**：受信ファイルを自動チェックする仕組み（外部APIやクラウドスキャン）を検討する。  
- **権限**：service account のスコープは最小限（例：`drive.file`）にする。  [oai_citation:14‡Google Cloud](https://cloud.google.com/iam/docs/service-account-overview?utm_source=chatgpt.com)

---

## よくある問題と対処
- 「ファイルがアップロードできない／500 が返る」→ Netlify の関数ログ（Deploy → Functions → logs）を確認。Google 認証エラーならサービスアカウント JSON の形式やスコープ、フォルダ共有を見直す。 [oai_citation:15‡Netlify Docs](https://docs.netlify.com/build/functions/deploy/?utm_source=chatgpt.com)  
- 「大きいファイルを送れない」→ クライアント側でサイズ制限（例 4MB）を設けるか、Drive の resumable upload を導入する。 [oai_citation:16‡Netlify Docs](https://docs.netlify.com/build/functions/overview/?utm_source=chatgpt.com)

---

## 追加の改善案（拡張）
- **プログレスバー**：大きいファイルは分割して送るか resumable を使って、クライアントで進捗を表示する。  
- **認証付きアップロード**：管理者のみアップロード可能にするなら、Netlify Identity や別の認証（JWT 等）を導入。  
- **メタデータ管理**：アップロード時にフォームデータ（説明、タグ等）を Drive のファイル説明や別テーブル（Google Sheets / Firestore）に保存。

---

## 参考（主要）
- Netlify Functions overview（制限・挙動）.  [oai_citation:17‡Netlify Docs](https://docs.netlify.com/build/functions/overview/?utm_source=chatgpt.com)  
- Get started with functions / Runtime（Node.js ランタイム周り）.  [oai_citation:18‡Netlify Docs](https://docs.netlify.com/build/functions/get-started/?utm_source=chatgpt.com)  
- Deploy functions（デプロイ方法：Git / CLI / API）.  [oai_citation:19‡Netlify Docs](https://docs.netlify.com/build/functions/deploy/?utm_source=chatgpt.com)  
- Google Drive API — Upload file data（resumable を含む）.  [oai_citation:20‡Google for Developers](https://developers.google.com/workspace/drive/api/guides/manage-uploads?utm_source=chatgpt.com)  
- サービスアカウントで Drive にアップロードする手順（フォルダ共有など実務ノウハウ）.  [oai_citation:21‡デジタルインスピレーション](https://www.labnol.org/google-api-service-account-220404?utm_source=chatgpt.com)


---

※この記事はChatGPTの回答を基に作成しています。

[[W02 Google Drive APIを使ったデプロイ手順（具体的解説）]]