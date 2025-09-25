[[W17 GitHub Codespacesで「Unable to open browser automatically」エラーが出たときの対処法]]

ジャンル・カテゴリ：ハウツー

[[#はじめに]]
[[#APIキーを.envファイルに保存する理由]]
[[#手順の全体像]]
[[#ステップ1 JSONファイルの準備]]
[[#ステップ2 .envファイルの作成と記述]]
[[#ステップ3 .gitignoreで除外する]]
[[#ステップ4 環境変数の読み込み]]
[[#まとめ]]

## はじめに
開発においてAPIキーなどの機密情報は公開リポジトリに含めないことが基本です。そのため、JSONファイルのままではなく、`.env`ファイルに保存し、`.gitignore`で除外することが推奨されます。この記事では、その具体的な流れをわかりやすく解説します。

## APIキーを.envファイルに保存する理由
- 公開リポジトリに誤ってアップロードしないようにするため
- 環境ごとに設定を切り替えやすくするため
- セキュリティリスクを最小限に抑えるため

## 手順の全体像
1. JSONファイルの中身を確認する
2. `.env`ファイルを作成し、必要な値を記述する
3. `.gitignore`に`.env`を追加する
4. コードから環境変数を読み込む

## ステップ1 JSONファイルの準備
まずは、Google CloudやFirebaseなどで取得したサービスアカウントのJSONファイルを用意します。例：
```json
{
  "type": "service_account",
  "project_id": "my-project-id",
  "private_key_id": "xxxx",
  "private_key": "-----BEGIN PRIVATE KEY-----\n....\n-----END PRIVATE KEY-----\n",
  "client_email": "xxxx@my-project-id.iam.gserviceaccount.com"
}
```

## ステップ2 .envファイルの作成と記述
プロジェクトのルートディレクトリに.envファイルを作成し、JSONファイル内の必要な値を環境変数として記述します。

```env
PROJECT_ID=my-project-id
PRIVATE_KEY_ID=xxxx
PRIVATE_KEY="-----BEGIN PRIVATE KEY-----\n....\n-----END PRIVATE KEY-----\n"
CLIENT_EMAIL=xxxx@my-project-id.iam.gserviceaccount.com
```
  
ポイント
- PRIVATE_KEYのように改行を含む値はダブルクォーテーションで囲む
- 値の前後に空白を入れない

## ステップ3 .gitignoreで除外する
.envファイルをリポジトリに含めないように、.gitignoreに以下を追加します。

```
.env
```

これにより、今後.envはGitにコミットされません。すでにコミット済みの場合はgit rm --cached .envでキャッシュから削除してください。

## ステップ4 環境変数の読み込み
アプリから環境変数を利用するためにはライブラリを使います。Node.jsの場合はdotenvをインストールします。

```bash
npm install dotenv
```

コード内で以下を記述します。
```js
require('dotenv').config();

console.log(process.env.PROJECT_ID);
console.log(process.env.CLIENT_EMAIL);
```

## まとめ
- JSONファイルの中身をそのまま公開しない
- .envに必要な値を移して保存
- .gitignoreで必ず除外
- ライブラリを使って安全に読み込む  
    この流れを守れば、APIキーを安全に管理できます。

---


※この記事はChatGPTの回答を基に作成しています。

[[W19 dotenvの読み込みコードはどこに書くべきか？]]
