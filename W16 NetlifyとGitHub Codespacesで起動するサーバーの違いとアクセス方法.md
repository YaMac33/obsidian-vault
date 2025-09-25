[[W15 GitHub Codespacesで`console.log(event.body)`の中身を確認する方法]]

ジャンル・カテゴリ：ハウツー

## 目次
[[#はじめに]]  
[[#NetlifyとCodespacesの違い]]  
[[#GitHub Codespacesでのサーバー起動と確認方法]]  
[[#ファイルアップロード後のログ確認手順]]  
[[#まとめ]]  

## はじめに
Web開発を進める際に「NetlifyでデプロイしたWebページ」と「GitHub Codespacesで起動したサーバー」を混同してしまうケースがあります。これらは性質が大きく異なり、アクセス方法やログの確認手順も違います。この記事では、両者の違いと、Codespacesで起動したサーバーへアクセスする方法を解説します。

## NetlifyとCodespacesの違い
### Netlify
- GitHubリポジトリのコードを自動でビルド＆デプロイ  
- 公開用の**本番サイト**として利用  
- URLは `https://xxxxx.netlify.app` のように提供される  
- Functions（サーバーレス関数）のログはNetlify管理画面から確認可能  

### GitHub Codespaces
- ブラウザ上やローカル環境で動く仮想開発環境  
- コードを編集しながらサーバーを**ローカルでテスト実行**できる  
- 起動したサーバーはCodespaces固有の**プレビュー用URL**でアクセス  

## GitHub Codespacesでのサーバー起動と確認方法
1. Codespacesを起動  
2. ターミナルで以下を実行（例：Netlify Functionsのローカル確認）  
   ```bash
   npm install -g netlify-cli
   netlify dev
   ```

3. 実行するとターミナルに以下のような出力が出る
```
◈ Starting Netlify Dev with functions...
◈ Functions server is listening on 8888
```
4. Codespacesの右上に「PORTS」タブがあり、使用中ポート（例：8888, 3000）が表示される
5. 「PORTS」タブで該当ポートをクリックすると、プレビューURLが発行され、ブラウザでアクセス可能

👉 Netlifyでの本番URLと違い、CodespacesのURLは開発者専用（例：https://8888-username-codespace.app.github.dev/）

## ファイルアップロード後のログ確認手順
- フロントエンドからファイルをアップロードすると、バックエンド（Netlify Functions相当）が処理を実行
- console.log(event.body) を書いておくと、Codespacesでの netlify dev 実行ターミナルにログが出力される
- 本番デプロイ後は、Netlify管理画面 > Functions > Logs で確認する

## まとめ
- Netlifyは本番用の公開サイト
- Codespacesはローカル開発・テスト環境
- Codespacesで起動したサーバーは「PORTS」タブ経由でアクセス
- アップロードやログ確認はローカルではターミナル、本番ではNetlify管理画面で確認可能


※この記事はChatGPTの回答を基に作成しています。