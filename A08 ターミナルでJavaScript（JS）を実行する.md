[[A07 ローカルで作成したブランチをリモートリポジトリで見るには]]

ターミナルでJavaScript（JS）を実行するには、**Node.js**を使用するのが一般的です。

Node.jsがインストールされていれば、以下のコマンドでJSファイルを実行できます。

### コマンド

`node [ファイル名]`

### 具体例

たとえば、`hello.js`という名前のファイルがあり、その中に以下のコードが書かれているとします。

**hello.js:**

JavaScript

```
console.log("Hello, Node.js!");
```

このファイルを実行するには、ターミナルで以下のコマンドを入力します。

`node hello.js`

実行すると、ターミナルには以下のように表示されます。

`Hello, Node.js!`

---

### Node.jsのインストール

もしNode.jsがまだインストールされていない場合は、まずインストールする必要があります。公式ウェブサイト（[https://nodejs.org/ja/](https://nodejs.org/ja/)）からダウンロードするか、Homebrew（Mac）、またはパッケージマネージャー（Linux）を使ってインストールできます。

- Macの場合（Homebrewを使用）
    
    brew install node
    
- WindowsやLinuxの場合
    
    公式ウェブサイトからインストーラーをダウンロードするのが最も簡単です。

[[A09 次回やること]]
