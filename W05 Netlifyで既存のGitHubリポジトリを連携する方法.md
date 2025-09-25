[[W04 Netlifyでアカウント作成から環境変数設定までの流れ]]

ジャンル・カテゴリ：ハウツー

[[#はじめに]]  
[[#全体の流れ]]  
[[#GitHubリポジトリの確認]]  
[[#Netlifyダッシュボードからの連携手順]]  
[[#ブランチとビルド設定]]  
[[#デプロイの確認]]  
[[#まとめ]]

## はじめに
NetlifyはGitHubリポジトリと連携することで、自動デプロイやビルドを簡単に行えるサービスです。ここでは既存のリポジトリ（例：https://github.com/YaMac33/file）をNetlifyに接続する具体的な手順を紹介します。

## 全体の流れ
- GitHubリポジトリの準備確認  
- Netlifyダッシュボードから新規サイト追加  
- リポジトリを選択して連携  
- ブランチとビルドコマンドを設定  
- デプロイ実行と動作確認  

## GitHubリポジトリの確認
- 対象リポジトリがGitHub上で公開・プライベートのいずれでもOK  
- mainブランチまたはmasterブランチなど、デプロイ対象のブランチを確認  
- 必要に応じて `index.html` などのエントリファイルを用意  

## Netlifyダッシュボードからの連携手順
- Netlifyにログイン後、「Add new site」→「Import an existing project」をクリック  
- 「Deploy with Git provider」で「GitHub」を選択  
- GitHubアカウントのアクセス許可を求められるので承認  
- リポジトリ一覧から `YaMac33/file` を選択  

## ブランチとビルド設定
- デプロイ対象のブランチ（例：`main`）を指定  
- 静的サイトの場合はビルドコマンドは不要  
- ReactやVueなどのフレームワークを使用している場合は以下を設定例にする  
  - Build command: `npm run build`  
  - Publish directory: `dist` または `build`  

## デプロイの確認
- 設定を完了すると自動で初回デプロイが開始  
- デプロイが成功するとランダムなNetlifyのドメインが割り当てられる  
- 「Site settings」からカスタムドメインの設定も可能  

## まとめ
- NetlifyとGitHubを連携させると自動デプロイが可能になる  
- 対象リポジトリを選んでブランチ・ビルド設定をすればすぐに公開できる  
- 今後はGitHubにpushするだけでNetlify側も更新される  

※この記事はChatGPTの回答を基に作成しています。

[[W06 GitHub PagesからNetlifyにデプロイしたWebページでアップロードがエラーになる原因とAIへの伝え方]]
