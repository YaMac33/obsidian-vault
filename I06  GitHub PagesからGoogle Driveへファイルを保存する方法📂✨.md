
[[I05 GitHub Pagesでファイルアップロードフォームを作成した場合の保存先について]]

## 🏷 ジャンル
- ハウツー / Web開発ガイド 🛠️

## 🌐 はじめに
GitHub Pages単体では直接Google Driveに保存する機能はありません。しかし、**Google Drive API**と**OAuth認証**を組み合わせることで、フォーム送信などからDriveへファイルを保存できます。ここでは、その実装手順と注意点を詳しく解説します💡。

## 📤 Google Driveを保存先にする方法
### 1️⃣ Google Drive APIを利用する🚀
- Google Cloud Consoleでプロジェクトを作成し、Drive APIを有効化する  
- OAuth 2.0 クライアントIDを発行し、クライアントシークレットを取得  
- フロントエンド（GitHub Pages側）からOAuth 2.0認証を行い、アクセストークンを取得  
- 取得したトークンを使って、fetchなどでDrive APIのfiles.createエンドポイントへリクエストを送信し、ファイルをアップロード  

### 2️⃣ セキュリティ上の注意🔒
- クライアントシークレットをそのままブラウザ側に埋め込まない  
- 代わりに、Cloud Functions・Cloudflare Workers・Netlify Functionsなどのサーバーレスバックエンドを経由して安全にトークンを管理  
- アップロード先のフォルダを制限したり、スコープを最小限に設定して不正利用を防止  

### 3️⃣ 実装のポイント💡
- Google APIのJavaScriptクライアントライブラリ（gapi）を使うと認証フローが簡単  
- 初回アクセス時にはユーザーの同意画面が表示される  
- ファイルアップロード後、Driveの共有リンクを返すなどの処理を追加すると便利  

## ✅ まとめ
- GitHub Pages単体ではDriveへの直接保存はできないが、**Google Drive APIとOAuth認証**を使えば実装可能✨  
- セキュリティ面を考慮し、**トークン管理はフロントエンドだけで完結させない**ことが重要  
- Google Cloud Consoleでの設定やAPIの知識が必要だが、これらの手順を踏めば、GitHub PagesのフォームからGoogle Driveを保存先として利用できる📂  

※この記事はChatGPTの回答を基に作成しています。

[[I07 セキュリティを考慮したトークン管理とサーバーレスバックエンドの活用法]]
