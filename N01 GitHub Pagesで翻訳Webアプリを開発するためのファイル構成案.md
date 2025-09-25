
## はじめに
本記事では、GitHub Pagesを利用して「翻訳Webアプリ」を開発する際のファイル構成案を解説します。ユーザーが日本語または英語のテキストを入力すると、対応する翻訳結果（英語または日本語）が表示されるシンプルな仕様を想定しています。データ保存機能は不要なため、フロントエンドのみで完結する構成となります。

ジャンル: **ハウツー記事**

## 全体構成
翻訳Webアプリは以下の3ファイルで構成します。

- `index.html` : アプリ本体の画面レイアウト
- `style.css` : デザインやレイアウト調整
- `script.js` : 入力テキストを処理し翻訳APIにリクエストを送るロジック

### ディレクトリ構成例
```
/my-translate-app
├── index.html
├── style.css
└── script.js
```

## 各ファイルの役割
### index.html
アプリのUIを定義するHTMLファイル。最低限以下を用意します。
- 入力テキスト用の`<textarea>`
- 翻訳ボタン
- 翻訳結果を表示するエリア

```html
<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8">
  <title>翻訳Webアプリ</title>
  <link rel="stylesheet" href="style.css">
</head>
<body>
  <h1>翻訳Webアプリ</h1>
  <textarea id="inputText" placeholder="日本語または英語を入力してください"></textarea>
  <button id="translateBtn">翻訳する</button>
  <div id="outputText"></div>
  <script src="script.js"></script>
</body>
</html>
```

### style.css
シンプルに見やすいレイアウトを整えます。
```css
body {
  font-family: sans-serif;
  margin: 2rem;
}
textarea {
  width: 100%;
  height: 100px;
}
#outputText {
  margin-top: 1rem;
  padding: 1rem;
  border: 1px solid #ccc;
  min-height: 50px;
}
```

### script.js
翻訳処理を行うJavaScriptファイル。GitHub Pagesはサーバー機能を持たないため、外部の翻訳API（例: Google Translate APIやLibreTranslate APIなど）を利用する想定です。
```JavaScript
document.getElementById("translateBtn").addEventListener("click", async () => {
  const inputText = document.getElementById("inputText").value;
  const output = document.getElementById("outputText");

  if (!inputText) {
    output.textContent = "テキストを入力してください。";
    return;
  }

  try {
    // LibreTranslate APIを例に利用
    const res = await fetch("https://libretranslate.de/translate", {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify({
        q: inputText,
        source: "auto",
        target: inputText.match(/[a-zA-Z]/) ? "ja" : "en"
      })
    });
    const data = await res.json();
    output.textContent = data.translatedText;
  } catch (error) {
    output.textContent = "翻訳に失敗しました。";
  }
});
```

### 補足: GitHub Pagesへのデプロイ手順
1. GitHubで新しいリポジトリを作成
2. 上記3ファイルをリポジトリに配置
3. GitHubのリポジトリ設定 → Pages → mainブランチを選択
4. デプロイ後に公開URLから利用可能

### まとめ
- GitHub Pagesではサーバーレス環境のため、外部翻訳APIを利用する形が最適
- index.html、style.css、script.jsの3ファイルでシンプルに構築可能
- データ保存は不要のため、構成は最小限で済む

※この記事はChatGPTの回答を基に作成しています。

[[N02 GitHub Pagesで文字数から読了時間を表示する方法]]
