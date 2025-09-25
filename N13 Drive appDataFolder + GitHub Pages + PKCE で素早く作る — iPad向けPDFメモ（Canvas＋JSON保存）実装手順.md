[[N12 noteブログで目次を作成する方法｜初心者向けステップガイド]]

ジャンル・カテゴリ: ハウツー

## 目次
[[#概要]]
[[#前提と選択理由]]
- 全体ハイレベルフロー
- 詳細手順（ステップ1 → ステップ7）
  - ステップ1: Google Cloud プロジェクト作成 & Drive API 有効化
  - ステップ2: OAuth 同意画面（Consent）設定（スコープとテストユーザ）
  - ステップ3: OAuthクライアント作成（Authorized JS origins / Redirect URIs）
  - ステップ4: クライアント側 — PKCE生成と認可リクエスト（コード例）
  - ステップ5: サーバレス交換エンドポイント（code → tokens、refresh保存）
  - ステップ6: Drive の `appDataFolder` に注釈JSONを保存／取得（API例）
  - ステップ7: GitHub Pages デプロイ・CORS・iPad 実機テスト
- テスト・運用チェックリスト
- iPad（Safari/PWA）向けの注意点（IndexedDB/保存の信頼性）
- よくある落とし穴と対策
- 補足（よく抜ける実装要素）
- 参考（公式ドキュメント等）
- 結論（短く）

## 概要
ここでは「GitHub Pages 上の静的フロントエンド（PDF.js + Canvas注釈）」「ユーザのGoogle Drive内の `appDataFolder` を注釈JSON保存領域に使う」「認証はPKCEを含むOAuth 2.0（必要に応じてサーバレスでコード交換してリフレッシュトークンを保管）」という構成で、**iPadで使える実装手順を最短で動かすための具体的手順**を示します。  
`appDataFolder` の特徴：アプリ専用の隠しフォルダとして、ユーザの Drive に格納され他のアプリやUIから見えません（アプリがアクセス可能）。 [oai_citation:0‡Google for Developers](https://developers.google.com/workspace/drive/api/guides/appdata?utm_source=chatgpt.com)

## 前提と選択理由
- GitHub Pages は静的ホスティング（SSLあり）で手早く公開できる。  
- ブラウザ（SPA）で直接Driveを操作すると「アクセス用トークンの寿命」「リフレッシュの扱い」「OAuth検証」の問題が出るため、**PKCE + 小さなサーバレス（コード受け取り・トークン交換）**を推奨する（オフライン/永続化で安全）。PKCEはブラウザ向けの推奨フローです。 [oai_citation:1‡oauth.net](https://oauth.net/2/pkce/?utm_source=chatgpt.com)

## 全体ハイレベルフロー
- フロント（GitHub Pages）: PDF表示（PDF.js） + 透明Canvasで注釈描画 → 注釈データをJSONに変換  
- 認証: ユーザがGoogleへログイン + 認可（PKCE） → 認可コードを受け取りサーバレスへ送信  
- サーバレス: 認可コード + code_verifier を使って `oauth2.googleapis.com/token` に交換 → access_token / refresh_token を安全に保存  
- Drive操作（サーバレス or フロント）: `files.create` / `files.update` を使って `appDataFolder` に JSON を保存・取得。 [oai_citation:2‡Google for Developers](https://developers.google.com/workspace/drive/api/guides/appdata?utm_source=chatgpt.com)

## 詳細手順（ステップ1 → ステップ7）

### ステップ1: Google Cloud プロジェクト作成 & Drive API 有効化
- Google Cloud Console に入り新規プロジェクトを作成。  
- 「API とサービス」→「ライブラリ」→ **Google Drive API** を有効化。 [oai_citation:3‡Google Cloud](https://cloud.google.com/sap/docs/abap-sdk/on-premises-or-any-cloud/latest/authentication-oauth-client-credentials?utm_source=chatgpt.com)
- 備考: プロジェクトIDは後でOAuth設定・請求情報で使うことがあるので控えておく。

### ステップ2: OAuth 同意画面（Consent）設定（スコープとテストユーザ）
- 「OAuth 同意画面」を作成（外部 / 内部 を選択）。アプリ名、サポートメール、プライバシーポリシーのURL（公開するなら必須）を記載。 [oai_citation:4‡Google for Developers](https://developers.google.com/workspace/guides/configure-oauth-consent?utm_source=chatgpt.com)
- **使うスコープの最小化**：注釈JSONを `appDataFolder` に保存するなら `https://www.googleapis.com/auth/drive.appdata` を検討。公開アプリで敏感/制限スコープを使う場合は審査（verification）が必要になるので、開発は「テストユーザ」に限定して進める。 [oai_citation:5‡Google for Developers](https://developers.google.com/identity/protocols/oauth2/production-readiness/sensitive-scope-verification?utm_source=chatgpt.com)

### ステップ3: OAuthクライアント作成（Authorized JS origins / Redirect URIs）
- 「認証情報」→「OAuth クライアントID を作成」→ アプリの種類は **Web アプリ**（もしくは「その他/Installed」だがGitHub PagesならWeb）。  
- **Authorized JavaScript origins** に `https://<your-username>.github.io`（またはカスタムドメイン）を追加。ローカル開発時は `http://localhost:5173` 等も追加。  
- **Authorized redirect URIs** に、PKCEで受け取るコールバック（例 `https://<your-username>.github.io/oauth2callback`）や、サーバレスのcallback（例 `https://your-fn.example.com/oauth/callback`）を正確に登録。URIは完全一致が必要。 [oai_citation:6‡Google Cloud](https://cloud.google.com/sap/docs/abap-sdk/on-premises-or-any-cloud/latest/authentication-oauth-client-credentials?utm_source=chatgpt.com)

### ステップ4: クライアント側 — PKCE生成と認可リクエスト（コード例）
- 流れ（クライアント側）:
  - code_verifier（ランダム文字列） を生成して `sessionStorage` に保存
  - code_challenge = BASE64URL( SHA256(code_verifier) )
  - 認可URL に `code_challenge` と `code_challenge_method=S256` を付与して Google の `/o/oauth2/v2/auth` にリダイレクト
- 代表的な JS（概念サンプル、実装はエラーハンドリング等を追加してください）
    const base64url = (buf) => btoa(String.fromCharCode(...new Uint8Array(buf))).replace(/\+/g,'-').replace(/\//g,'_').replace(/=+$/,'');
    async function sha256(message){ const msgUint8 = new TextEncoder().encode(message); const hashBuffer = await crypto.subtle.digest('SHA-256', msgUint8); return hashBuffer; }
    function randomString(len = 64){ const arr = new Uint8Array(len); crypto.getRandomValues(arr); return Array.from(arr).map(b=>('0'+b.toString(16)).slice(-2)).join(''); }
    async function makePKCEandRedirect(clientId, redirectUri, scope){
      const verifier = randomString(64);
      sessionStorage.setItem('pkce_verifier', verifier);
      const challengeBuf = await sha256(verifier);
      const challenge = base64url(challengeBuf);
      const url = new URL('https://accounts.google.com/o/oauth2/v2/auth');
      url.searchParams.set('client_id', clientId);
      url.searchParams.set('redirect_uri', redirectUri);
      url.searchParams.set('response_type', 'code');
      url.searchParams.set('scope', scope);
      url.searchParams.set('code_challenge', challenge);
      url.searchParams.set('code_challenge_method', 'S256');
      url.searchParams.set('access_type', 'offline'); // 別途サーバ側で交換して refresh を得る設計にするなら使用
      window.location = url.toString();
    }
- 重要: `access_type=offline` をつけるとリフレッシュトークン発行を促せますが、ブラウザ内だけで完結させると **refresh token の安全な保管ができない**ため、サーバ側で交換して保存する設計を推奨します。 [oai_citation:7‡Google for Developers](https://developers.google.com/identity/protocols/oauth2/web-server?utm_source=chatgpt.com)

### ステップ5: サーバレス交換エンドポイント（code → tokens、refresh保存）
- 理由：ブラウザに client_secret を置きたくない（安全性）、refresh_token を安全に保管して長期アクセスするため。  
- 最小実装（Node.js / Express のサンプルイメージ、Netlify/Vercel のサーバレスへ移植可）
    const fetch = require('node-fetch');
    app.post('/oauth/exchange', async (req, res) => {
      const { code, code_verifier, redirect_uri } = req.body;
      const params = new URLSearchParams();
      params.set('client_id', process.env.GOOGLE_CLIENT_ID);
      params.set('client_secret', process.env.GOOGLE_CLIENT_SECRET);
      params.set('code', code);
      params.set('code_verifier', code_verifier);
      params.set('grant_type', 'authorization_code');
      params.set('redirect_uri', redirect_uri);
      const tokenRes = await fetch('https://oauth2.googleapis.com/token', { method:'POST', headers:{'Content-Type':'application/x-www-form-urlencoded'}, body: params });
      const tokenJson = await tokenRes.json();
      // tokenJson.access_token / tokenJson.refresh_token を安全にDBに保存（ユーザIDと紐づけ）
      res.json(tokenJson);
    });
- 備考:
  - サーバ側は `GOOGLE_CLIENT_SECRET` を環境変数等で安全に持つ。  
  - 取得した `refresh_token` をDB（例：Supabase / Firestore / DynamoDB）にユーザIDで保存し、必要時に新しい access_token を取得して Drive API を叩く。  
  - サーバレス関数に CORS 設定と SSL（HTTPS）を必須にする。

### ステップ6: Drive の `appDataFolder` に注釈JSONを保存／取得（API例）
- 基本方針:
  - 注釈は軽量な JSON（ページごとに配列、ベクタ形式で座標＋色＋太さ＋タイムスタンプ）にし、`appDataFolder` にファイルとして保存。  
  - ファイル名のルール例： `annotations_<pdfFileId>.json`（同名存在チェック→update、無ければcreate）。  
- 重要APIポイント: `appDataFolder` に作るには `parents: ['appDataFolder']` を指定して `files.create` を呼ぶ。 [oai_citation:8‡Google for Developers](https://developers.google.com/workspace/drive/api/guides/appdata?utm_source=chatgpt.com)
- シンプルな保存ロジック（フロント or サーバ経由での実行例）
  - 1) 既存ファイル検索（files.list?q=name='annotations_XXX.json' & spaces=appDataFolder）
  - 2a) 存在しなければ `POST https://www.googleapis.com/upload/drive/v3/files?uploadType=multipart` で metadata+content を multipart/related で送る
  - 2b) 存在すれば `PATCH https://www.googleapis.com/upload/drive/v3/files/{fileId}?uploadType=multipart` で上書き
- （Fetch + multipart例／概念）
    // metadata
    const meta = { name: filename, parents: ['appDataFolder'], mimeType: 'application/json' };
    const boundary = '-------314159265358979323846';
    const body = `--${boundary}\r\nContent-Type: application/json; charset=UTF-8\r\n\r\n${JSON.stringify(meta)}\r\n--${boundary}\r\nContent-Type: application/json\r\n\r\n${jsonContent}\r\n--${boundary}--`;
    fetch('https://www.googleapis.com/upload/drive/v3/files?uploadType=multipart', {
      method: 'POST',
      headers: { 'Authorization': `Bearer ${accessToken}`, 'Content-Type': `multipart/related; boundary=${boundary}` },
      body
    });
  - 詳細なアップロード仕様と `uploadType`（simple/multipart/resumable）については公式ガイド参照。 [oai_citation:9‡Google for Developers](https://developers.google.com/workspace/drive/api/guides/manage-uploads?utm_source=chatgpt.com)

### ステップ7: GitHub Pages デプロイ・CORS・iPad 実機テスト
- GitHub Pages に静的ファイル（index.html, main.js, pdfjs, UI）を push → ページ公開。  
- OAuth の `Authorized JavaScript origins` / `redirect URIs` に公開URLを正確に登録。ローカル開発用にも `localhost` を追加しておく。 [oai_citation:10‡Google Cloud](https://cloud.google.com/sap/docs/abap-sdk/on-premises-or-any-cloud/latest/authentication-oauth-client-credentials?utm_source=chatgpt.com)
- CORS: サーバレス（token交換やDrive呼び出しをサーバ経由で行う場合）では `Access-Control-Allow-Origin` を適切に返す。フロントから直接 Drive の REST を呼ぶ場合は Authorization ヘッダのみで動く（ただしブラウザ側でCORSの影響は基本的にないが、サーバとの連携はCORS注意）。  
- iPad 実機テスト: Safari と Chrome (WKWebViewベース) 両方で動作確認。PWA化（ホーム画面追加）するとブラウザのストレージ挙動が変わるので注意。

## テスト・運用チェックリスト
- OAuth同意画面のスコープが実装と一致しているか（コードが要求するスコープとConsentで設定したスコープ）。 [oai_citation:11‡Google for Developers](https://developers.google.com/workspace/guides/configure-oauth-consent?utm_source=chatgpt.com)
- `appDataFolder` に create/list/update ができるか（テストユーザで確認）。 [oai_citation:12‡Google for Developers](https://developers.google.com/workspace/drive/api/guides/appdata?utm_source=chatgpt.com)
- トークンの有効期限切れ時にサーバで refresh が行えるか（サーバ実装の確認）。 [oai_citation:13‡Google for Developers](https://developers.google.com/identity/protocols/oauth2/web-server?utm_source=chatgpt.com)
- iPad で IndexedDB の自動削除（7日ルール等）を考慮しているか（ローカルはあくまでキャッシュ）。 [oai_citation:14‡WebKit](https://webkit.org/blog/14403/updates-to-storage-policy/?utm_source=chatgpt.com)

## iPad（Safari/PWA）向けの注意点
- **IndexedDB / localStorage は iOS Safari で自動削除される可能性がある（7日などの未使用でEviction）**。よってローカルは「短期キャッシュ」とし、注釈の最終版は必ずクラウド（Drive）へ同期する運用にすること。`navigator.storage.persist()` の呼び出しや、PWAとしてホーム画面に追加してもらう運用オプションを検討。 [oai_citation:15‡WebKit](https://webkit.org/blog/14403/updates-to-storage-policy/?utm_source=chatgpt.com)

## よくある落とし穴と対策
- 「ブラウザだけで完結させて refresh_token を期待」 → 多くのケースで refresh_token は安全に扱えないためサーバ側で管理する設計が必要。 [oai_citation:16‡Google for Developers](https://developers.google.com/identity/protocols/oauth2/web-server?utm_source=chatgpt.com)  
- 「OAuth 同意画面で未登録のスコープを要求してエラー」 → 同意画面で使うスコープを全て登録・保存すること。 [oai_citation:17‡Google for Developers](https://developers.google.com/workspace/guides/configure-oauth-consent?utm_source=chatgpt.com)  
- 「iPadでIndexedDBが消えてユーザの注釈が消えた」 → クラウド同期を必須にする運用／自動エクスポート（ユーザにダウンロードさせる機能）を用意する。

## 補足（よく抜ける実装要素）
- **バージョン管理**：注釈JSONに `version` と `updatedAt` を入れて、マージや衝突解消ロジックを用意する。  
- **エクスポート／インポート**：ユーザが手動で JSON をダウンロード・アップロードできる機能を用意（データ喪失保険）。  
- **暗号化**：機密注釈を扱うならクライアントで AES 暗号化して JSON を保存（サーバ保管時も暗号化済み）する運用を検討。  
- **Undo/Redo / 圧縮**：注釈の増加を抑えるため、ベクトルで保存し長さや座標を圧縮（簡易差分）する工夫。

## 参考（公式ドキュメント等）
- Google Drive `appDataFolder`（アプリ用隠しフォルダ）.  [oai_citation:18‡Google for Developers](https://developers.google.com/workspace/drive/api/guides/appdata?utm_source=chatgpt.com)  
- Google Identity Services — Use Code Model（ブラウザでの認可 / コードモデル）.  [oai_citation:19‡Google for Developers](https://developers.google.com/identity/oauth2/web/guides/use-code-model?utm_source=chatgpt.com)  
- OAuth 2.0（PKCE / RFC 7636）.  [oai_citation:20‡oauth.net](https://oauth.net/2/pkce/?utm_source=chatgpt.com)  
- Drive ファイルアップロード（uploadType: multipart / resumable 等）ガイド.  [oai_citation:21‡Google for Developers](https://developers.google.com/workspace/drive/api/guides/manage-uploads?utm_source=chatgpt.com)  
- OAuth 同意画面・スコープ設定（verification の注意）.  [oai_citation:22‡Google for Developers](https://developers.google.com/workspace/guides/configure-oauth-consent?utm_source=chatgpt.com)  
- Safari / WebKit のストレージポリシー・永続化ガイド（IndexedDB 自動削除等）.  [oai_citation:23‡WebKit](https://webkit.org/blog/14403/updates-to-storage-policy/?utm_source=chatgpt.com)

## 結論（短く）
- **短時間で動くプロトタイプ**：フロントで PKCE を行い、認可コードをサーバレスに送ってトークン交換（refresh を保存）→ Drive `appDataFolder` に注釈JSONを保存する構成が現実的・安全です。 [oai_citation:24‡Google for Developers](https://developers.google.com/workspace/drive/api/guides/appdata?utm_source=chatgpt.com)  
- **iPad運用の鍵**：ローカルはキャッシュ、最終保存はDriveに自動同期。PWA化や `navigator.storage.persist()` の利用を検討。 [oai_citation:25‡WebKit](https://webkit.org/blog/14403/updates-to-storage-policy/?utm_source=chatgpt.com)

※この記事はChatGPTの回答を基に作成しています。