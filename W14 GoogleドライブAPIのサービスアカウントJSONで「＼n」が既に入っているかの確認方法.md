[[W13 Netlifyの環境変数にGoogle Drive APIのJSONを設定する方法]]

ジャンル・カテゴリ：ハウツー

[[#はじめに]]
[[#質問のポイント]]
[[#具体的にどこにnが入るのか]]
[[#このJSONは既に正しい形か]]
[[#まとめ]]

## はじめに
Google Drive APIのサービスアカウントJSONをNetlifyやVercelの環境変数に登録する際、「改行を `\n` に置き換える」と説明されることがあります。  
では、実際にGoogleが発行するJSONにはすでに `\n` が入っているのでしょうか？

## 質問のポイント
ユーザーが取得したJSONには、以下のような記述が含まれています。

```json
"private_key": "-----BEGIN PRIVATE KEY-----\n…….\n…..\n……..avFqlQfWJo06rD+rRu2jSCEXuS7lbA\nXhMU,,,,,,…ygn/KJMud+．,,,,,,,,,,,,,wFi\nn,,,,,,,,,,,,,3xA\nKLa,,,,,,,,,BG\ngz6o,,,,,,LYQ1Y\n...
-----END PRIVATE KEY-----\n"
```

この「`\n`」が重要です。

## 具体的にどこに\nが入るのか
- `private_key` の中で改行が必要な場所には **すでに `\n` が入っている**  
- 例えば以下の部分：
  - `"-----BEGIN PRIVATE KEY-----\n"`
  - `"……avFqlQfWJo06rD+rRu2jSCEXuS7lbA\nXhMU..."` の改行箇所
  - `"-----END PRIVATE KEY-----\n"`

つまり、元の秘密鍵ファイルに存在していた改行が、そのままエスケープシーケンス `\n` として保存されています。

## このJSONは既に正しい形か
はい、このJSONは既に「改行を `\n` に置き換えた形」になっています。  
- Googleが発行するサービスアカウントJSONは、最初からその形式です。  
- **追加で置換する必要はありません**。  
- そのままNetlifyの環境変数に貼り付ければ動作します。  

注意点としては、環境変数に貼り付けるときに余計な改行やスペースを入れないことだけです。

## まとめ
- サービスアカウントJSONの `private_key` 部分は、Googleが発行した時点で **すでに改行が `\n` に変換されている**  
- ユーザーのJSONもすでにその状態になっているので、追加で変換する必要はない  
- NetlifyやVercelでは **そのまま1行として貼り付け** ればOK  

※この記事はChatGPTの回答を基に作成しています。

[[W15 GitHub Codespacesで`console.log(event.body)`の中身を確認する方法]]