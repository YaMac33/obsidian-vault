[[W06 GitHub PagesからNetlifyにデプロイしたWebページでアップロードがエラーになる原因とAIへの伝え方]]

ジャンル・カテゴリ: ハウツー
[[#はじめに]]
[[#ログの全体像]]
[[#各セクションの読み方]]
[[#「Site is live」なのにNot Foundになる主な原因]]
[[#優先チェックリスト（まずこれを試す）]]
[[#ケース別の深掘りと対処法]]
[[#Netlify Functionsについて（今回のログから読み取れること）]]
[[#追加で必ず確認・追記すべき手順（不足分の補足）]]
[[#まとめ]]

## はじめに
Netlifyのビルドログを貼って「正常にデプロイされたか？」と確認したいケース向けに、ログの読み方と「Site is live ✨」なのにブラウザから開くと Not Found になる主な原因・対処手順をわかりやすく解説します。ログ自体は成功を示していますが、表示されない理由は設定や公開ディレクトリの中身にあることが多いです。

## ログの全体像
- Initializing（初期化）: キャッシュ復元、Node/Ruby/Goなどのランタイム準備。
- Building（ビルド）: フレームワーク検出、ビルドコマンドの実行、Functions のバンドル、秘密情報スキャン。
- Deploying（デプロイ）: 公開するディレクトリ（今回: `docs`）からファイルをアップロード。
- Cleanup / Post-processing（後処理）: キャッシュ保存、ヘッダ／リダイレクト適用、フォーム検出等。
- 最後に "Site is live ✨" が出ていれば、Netlify側のデプロイ処理は終わって「公開状態」と判断されています。

## 各セクションの読み方
### Initializing
- ログ例: `Fetching cached dependencies` / `Finished extracting cache`
- 意味: 過去のビルドキャッシュを復元して高速化しています。問題ではない。

### Building
- `v22.19.0 is already installed.` → Node環境が使える状態。
- `Detected 0 framework(s)` → Netlifyが自動的に検出したフレームワークがないため、自動ビルドの推奨設定（自動ビルドコマンド）が走らない可能性があります。静的ファイルだけを`docs`に置いているならそれでOKだが、ビルドが必要な場合は明示的に `build` コマンドを設定する必要があります。
- `Packaging Functions from netlify/functions directory` → Functions（サーバレス）がバンドルされています。これは静的サイトの表示には直接関係しませんが、API的に使えます。
- `Secrets scanning complete` → ログに秘密鍵が混入していないかのチェックが走っています。

### Deploying
- `Starting to deploy site from 'docs'` → Netlifyは `docs` フォルダを公開ルートに指定している。
- `0 new file(s) to upload` → ビルド出力（docs）に変更がない、あるいは中身が空であるため何もアップロードしない状態です。過去と同一ファイルなら「差分0」で終了します。

### Post-processing
- `Site is live ✨` → デプロイ処理そのものは成功。ただし「公開ファイルが正しく配置されているか」は別問題。

## 「Site is live」なのにNot Foundになる主な原因
- publish（公開）ディレクトリが間違っている／空になっている（今回ログは `docs`）。
- `docs` 内に `index.html` がない（サーバが返すデフォルトページがないため 404）。
- SPA（React/Vue等）で `build` が実行されておらず、静的ビルド結果（`dist` や `build`）が `docs` に入っていない。
- クライアントサイドルーティングで `/_routes_` にアクセスすると 404 になる（`_redirects` が未設定）。
- カスタムドメインや DNS 設定が正しくない — ドメインはサイトに紐付くが DNS が未設定だと接続不可／Not Found 表示になることがある。
- ブランチ / base directory の設定ミス（`baseRelDir: true` が示すようにサブディレクトリ運用している場合の設定不整合）。
- キャッシュや差分検出のせいで「0 new files」になり、実際はファイルが存在しない／生成されていない。
- リダイレクト設定（`_redirects` や `netlify.toml`）が原因で意図せぬパスに飛ばされている。

## 優先チェックリスト（まずこれを試す）
- Netlify の「Deploys」ページで最新のデプロイを開き、`Browse deploy`（または production URL）をクリックして正しい URL を確認する。
- 公開ディレクトリの中身を確認する（ローカルでリポジトリをクローンして以下を実行）:
  - `ls -la docs` で `index.html` などファイルがあるか確認。
  - ローカルテスト: `cd docs` → `python -m http.server 8080` または `npx serve .` でブラウザから中身を確認。
- Netlify のサイト設定（Site settings → Build & deploy）で `Publish directory` が `docs` になっているか確認。`netlify.toml` があるなら `publish` の記述を確認。
- SPA の場合は `_redirects` を追加（例: `/* /index.html 200`）。
- ビルドが必要なプロジェクトなら `Build command` を設定（例: `npm run build`）し、`publish` をビルド出力フォルダに合わせる（例: `build`／`dist`）。
- 「Clear cache and deploy site」（Netlify UI のトリガー）で再ビルドしてみる（`0 new files` を解消するため）。
- カスタムドメインを使っている場合は DNS が正しく向いているか確認（A/ALIAS/CNAME 設定）。

## ケース別の深掘りと対処法
### publishディレクトリが空／間違っている
- 対処:
  - `netlify.toml` または UI で `publish` を正しく設定する。
  - 例（netlify.toml）:
    -     [build]
    -       publish = "docs"
    -       command = "npm run build"
  - `docs` に `index.html` が無ければ追加するか、ビルドコマンドで出力するようにする。

### シングルページアプリ（SPA: React/Vue等）
- 問題: ルーティングで直接アクセスすると 404 になる。
- 対処: `docs/_redirects` ファイルを追加して次を記述:
  -     /*  /index.html  200
- またビルドコマンドと publish ディレクトリを正しく設定する（例: `npm run build` → `publish = "dist"`）。

### カスタムドメインでNot Found
- 確認:
  - Netlify 側でドメインが有効化されているか。
  - DNS の A / CNAME が Netlify の指定に沿っているか。
  - DNS の反映に時間がかかる（最大で数時間〜24時間）。

### ビルドコマンドが走っていない／フレームワーク検出ゼロ
- ログに `Detected 0 framework(s)` は自動検出にヒットしていないだけ。手動で `build command` を指定すればOK。
- `package.json` にスクリプトを用意:
  -     "scripts": {
  -       "build": "your-build-tool build"
  -     }

## Netlify Functionsについて（今回のログから読み取れること）
- `Packaging Functions from netlify/functions directory: - upload.js` とあり、`upload.js` をバンドルしてFunctionsにアップしています。
- 注意点:
  - Functions は API 的に動き、静的ファイルの `index.html` が無いとブラウザのトップ表示はできません。
  - Functions のエラーは通常ブラウザの 500 や関数呼出し時の 404/500 を生むが、静的ページの「Not Found」とは別問題。

## 追加で必ず確認・追記すべき手順（不足分の補足）
- `index.html` が必須: 公開サイトのトップに表示する `index.html` を `publish` ディレクトリ直下に置く。
- SPA 対応: `_redirects` の追加を忘れずに。
- netlify.toml の最低例:
  -     [build]
  -       command = "npm run build"
  -       publish = "docs"
  -     [functions]
  -       directory = "netlify/functions"
- ローカル確認フロー（推奨）:
  - リポジトリをローカルにクローン
  - 必要なら `npm install` → `npm run build`
  - 出力先（例: `docs`）に `index.html` があることを確認
  - `cd docs` → `python -m http.server 8080` でブラウザから確認
- Netlify CLI で強制デプロイ（任意）:
  - `npm i -g netlify-cli`
  - `netlify deploy --prod --dir=docs` （初回はログイン/コネクトが必要）
- キャッシュが原因のとき:
  - Netlify UI の「Trigger deploy」→「Clear cache and deploy site」を実行して強制的に再ビルド。

## まとめ
今回のログは Netlify がビルド→デプロイを正常に終え「Site is live ✨」と出しているので、Netlify の処理自体は成功しています。にもかかわらずブラウザで Not Found になる主因は「公開ディレクトリ（今回 `docs`）に公開用ファイルがない／正しく出力されていない」「publish ディレクトリの設定ミス」「SPA のルーティング設定不足」「カスタムドメインの DNS 設定不備」などが考えられます。優先的に `docs` の中身（`index.html`）と Netlify の `publish` 設定、必要ならビルドコマンドや `_redirects` の有無を点検し、キャッシュクリアで再デプロイしてください。

※この記事はChatGPTの回答を基に作成しています。

[[W08 エラー「Internal Server Error "undefined" is not valid JSON」とは？]]