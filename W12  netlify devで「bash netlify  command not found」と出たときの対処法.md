[[W11 `console.log`を追加してリクエスト内容を確認する方法]]

ジャンル・カテゴリ：ハウツー

[[#はじめに]]
[[#エラーの原因]]
[[#解決方法]]
[[#補足: Node.jsとnpmの確認]]
[[#まとめ]]

## はじめに
Netlifyでローカル開発を行う際に便利なのが `netlify dev` コマンドです。  
しかし、環境によってはターミナルで次のようなエラーが出ることがあります。

```
bash: netlify: command not found
```

このエラーの原因と解決方法を解説します。

## エラーの原因
このエラーは、シンプルに「Netlify CLI（Command Line Interface）がインストールされていない」か、「PATHに通っていない」ことが原因です。

## 解決方法
### 1. Netlify CLIをインストールする
npm（Node.jsに付属）を使ってインストールします。

- グローバルインストール
```bash
npm install -g netlify-cli
```

- プロジェクトごとのローカルインストール
```bash
npm install --save-dev netlify-cli
```

### 2. インストール確認
インストール後に次を実行して確認します。
```bash
netlify --version
```
バージョン番号が表示されればOKです。

### 3. コマンド実行
ローカルサーバーを起動するには以下を実行します。
```bash
netlify dev
```

## 補足: Node.jsとnpmの確認
Netlify CLIを使うにはNode.js環境が必要です。  
次のコマンドで確認しましょう。

```bash
node -v
npm -v
```

- Node.jsがインストールされていない場合 → [Node.js公式サイト](https://nodejs.org/)からLTS版をインストール  
- npmが古い場合 → `npm install -g npm` でアップデート可能

## まとめ
- `bash: netlify: command not found` は **Netlify CLIが未インストール** で起こる  
- 解決策は `npm install -g netlify-cli` でCLIを入れること  
- Node.js環境が整っているかも合わせて確認するとスムーズ  

# netlify devで起動したサーバーを停止する方法

ジャンル・カテゴリ：ハウツー

[[#はじめに]]
[[#サーバー停止の基本操作]]
[[#強制終了が必要な場合]]
[[#まとめ]]

## はじめに
`netlify dev` コマンドを実行すると、ローカル環境でNetlify Functionsや静的サイトを確認できるサーバーが起動します。  
開発が終わったら、このサーバーを停止する必要があります。ここではその方法を解説します。

## サーバー停止の基本操作
ターミナルでサーバーを実行中に、次の操作を行います。

- **Windows / macOS / Linux 共通**
  - キーボードで `Ctrl + C` を押す

これでプロセスが終了し、サーバーが停止します。

## 強制終了が必要な場合
まれに `Ctrl + C` で止まらない場合があります。その場合は以下の方法を試します。

- **Linux / macOS**
  ```bash
  lsof -i :8888   # netlify dev のデフォルトポートを使用しているプロセスを探す
  kill -9 <プロセスID>
  ```

- **Windows**
  ```powershell
  netstat -ano | findstr :8888
  taskkill /PID <プロセスID> /F
  ```

※ `8888` はデフォルトポートです。実際に使っているポート番号に置き換えてください。

## まとめ
- `netlify dev` で起動したサーバーは **Ctrl + C** で停止可能  
- それでも止まらない場合は、ポートを使用しているプロセスを調べて手動で終了  
- 開発が終わったら必ず停止しておくと、他の開発でポート競合を防げる  

※この記事はChatGPTの回答を基に作成しています。

[[W13 Netlifyの環境変数にGoogle Drive APIのJSONを設定する方法]]