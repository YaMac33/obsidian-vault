[[W03 ターミナルで実行する `npm install` とは何か？ — 仕組み・使い方・注意点を丁寧に解説]]

ジャンル・カテゴリ：ハウツー

[[#はじめに]]  
[[#全体の流れ]]  
[[#Netlifyアカウントの作成]]  
[[#リポジトリの準備と連携]]  
[[#Netlify Functionsの配置]]  
[[#GoogleサービスアカウントJSONの準備]]  
[[#Netlifyで環境変数を設定する]]  
[[#デプロイと動作確認]]  
[[#まとめ]]

## はじめに
GitHub Pages と Netlify Functions を組み合わせ、Google Drive へのファイルアップロードを実現するには、Netlify 側の準備が必要です。本記事では、アカウント作成から環境変数の設定までを具体的に解説します。

## 全体の流れ
- Netlify アカウント作成  
- GitHub リポジトリの準備と連携  
- Netlify Functions の配置  
- Google サービスアカウント JSON の用意  
- 環境変数の登録（サービスアカウントJSON・フォルダIDなど）  
- デプロイと確認  

## Netlifyアカウントの作成
- [Netlify公式サイト](https://www.netlify.com/) にアクセス  
- 「Sign Up」から GitHub アカウント連携で登録すると便利  
- ダッシュボードにログインできることを確認  

## リポジトリの準備と連携
- Functions を配置する GitHub リポジトリを用意  
  - Webサイト用と同一リポジトリでも可  
  - Functions 専用に別リポジトリを作成しても可  
- Netlify ダッシュボード → 「Add new site」 → 「Import an existing project」からリポジトリを選択  
- デプロイ対象のブランチを指定（通常は `main`）  

## Netlify Functionsの配置
- リポジトリ直下に `netlify/functions` ディレクトリを作成  
- 例：`netlify/functions/upload.js`  
```javascript
exports.handler = async (event) => {
  return {
    statusCode: 200,
    body: JSON.stringify({ message: "Function is working!" })
  };
};
```
- プッシュすると自動でビルド＆デプロイされる

## GoogleサービスアカウントJSONの準備
- Google Cloud Console で新規プロジェクトを作成  
- 「APIとサービス」→「認証情報」→「サービスアカウント」作成  
- Google Drive API を有効化  
- JSONキーをダウンロードし、文字列化して利用する  
- 例：ファイル全体をコピーして環境変数に登録可能  

## Netlifyで環境変数を設定する
- Netlify ダッシュボード → 対象サイト → 「Site settings」 → 「Environment variables」  
- 以下を登録  
  - `GOOGLE_SERVICE_ACCOUNT`：サービスアカウントJSON（全体を文字列化して貼り付け）  
  - `GOOGLE_DRIVE_FOLDER_ID`：アップロード先フォルダID  
- 保存後は再デプロイを実施  

## デプロイと動作確認
- GitHub に push → Netlify が自動でデプロイ  
- `/.netlify/functions/upload` にアクセスし動作確認  
- エラーログは Netlify の「Functions」タブから確認可能  

## まとめ
- Netlify アカウントを作成し、GitHub リポジトリを連携  
- Functions は `netlify/functions` 配下に配置  
- Google サービスアカウント JSON とフォルダIDを環境変数に設定  
- デプロイ後、エンドポイントから動作確認できる  

※この記事はChatGPTの回答を基に作成しています。

[[W05 Netlifyで既存のGitHubリポジトリを連携する方法]]
