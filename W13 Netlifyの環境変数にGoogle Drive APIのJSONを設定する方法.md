
[[W12  netlify devで「bash netlify  command not found」と出たときの対処法]]

ジャンル・カテゴリ：ハウツー

[[#はじめに]]
[[#サービスアカウントキーの構造]]
[[#Netlify環境変数への設定方法]]
[[#よくあるミス]]
[[#まとめ]]

## はじめに
Google Drive APIをNetlify Functionsで利用するには、Google Cloud Consoleで発行した**サービスアカウントキー（JSON形式）**をNetlifyの環境変数に設定する必要があります。  
ここでは「JSONファイルをそのまま貼り付けるのか？」という疑問に答えながら、正しい設定方法を解説します。

## サービスアカウントキーの構造
Google Cloud Consoleで発行されるJSONには、以下のようなフィールドが含まれています。

- `type`
- `project_id`
- `private_key_id`
- `private_key`
- `client_email`
- `client_id`
- `auth_uri`
- `token_uri`
- `auth_provider_x509_cert_url`
- `client_x509_cert_url`

特に重要なのは  
- `client_email`（認証に必要）  
- `private_key`（署名に必要）  

この2つを含む完全なJSONを環境変数として保持する必要があります。

## Netlify環境変数への設定方法
Netlifyに設定する方法は以下のとおりです。

1. Netlify管理画面の **「Site settings」 → 「Build & deploy」 → 「Environment」** を開く  
2. **「Environment variables」** セクションで新しい変数を追加  
3. 名前を例として `GOOGLE_SERVICE_ACCOUNT_KEY` にする  
4. JSONファイルの中身を**すべてコピーして貼り付ける**

例：
```json
{
  "type": "service_account",
  "project_id": "my-project-id",
  "private_key_id": "xxxxxxxxxxxxxxxxxxxx",
  "private_key": "-----BEGIN PRIVATE KEY-----\nABCDEF...\n-----END PRIVATE KEY-----\n",
  "client_email": "xxxx@my-project.iam.gserviceaccount.com",
  "client_id": "1234567890",
  "auth_uri": "https://accounts.google.com/o/oauth2/auth",
  "token_uri": "https://oauth2.googleapis.com/token",
  ...
}
```

### 注意点
- `private_key` 内の **改行は `\n` に置き換えられている**ことを確認してください。
- そのまま貼り付けてもNetlify側で保持できますが、エディタによっては改行が壊れることがあるため注意が必要です。

## よくあるミス
- JSONの一部だけ（`client_email`や`private_key`のみ）を貼り付けてしまう → 認証エラー  
- 改行コードが壊れて `"invalid JSON"` エラーになる  
- JSONの外側に余計な文字やコメントを入れてしまう  

## まとめ
- **Google Drive APIのサービスアカウントJSONは全文をNetlify環境変数に貼り付ける必要があります。**  
- 変数名は `GOOGLE_SERVICE_ACCOUNT_KEY` のようにわかりやすく設定するのがおすすめです。  
- 改行やフォーマット崩れに注意して正しく貼り付ければ、`process.env.GOOGLE_SERVICE_ACCOUNT_KEY` でそのまま利用できます。

※この記事はChatGPTの回答を基に作成しています。