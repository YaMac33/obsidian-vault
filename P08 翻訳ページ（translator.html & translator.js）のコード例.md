[[P07 電卓ページ（calculator.html & calculator.js）のコード例]]

ジャンル・カテゴリ：ハウツー

## 目次
[[#はじめに]]  
[[#translatorhtml]]  
[[#translatorjs]]  
[[#まとめ]]  

## はじめに
ここでは、ツール集の一つである **翻訳ページ** を実装するための `translator.html` と `translator.js` のサンプルコードを紹介します。シンプルに日本語⇔英語翻訳を想定したUIを作成し、サンプルとして内部に簡易辞書を組み込んで動作確認できるようにしています。本格的に利用する場合はGoogle Translate APIなど外部サービスを組み込むことも可能です。

## translator.html
```html
<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>翻訳 - Toolbox</title>
  <link rel="stylesheet" href="../css/style.css">
  <link rel="stylesheet" href="../css/tools.css">
</head>
<body>
  <header>
    <h1>翻訳ツール</h1>
    <nav>
      <a href="../index.html">← トップへ戻る</a>
    </nav>
  </header>
  <main>
    <section class="translator">
      <label for="inputText">翻訳したいテキスト:</label>
      <textarea id="inputText" rows="5" placeholder="ここに文章を入力してください"></textarea>
      <div class="options">
        <label for="langFrom">翻訳元:</label>
        <select id="langFrom">
          <option value="ja">日本語</option>
          <option value="en">英語</option>
        </select>
        <label for="langTo">翻訳先:</label>
        <select id="langTo">
          <option value="en">英語</option>
          <option value="ja">日本語</option>
        </select>
      </div>
      <button id="translateBtn">翻訳する</button>
      <h2>翻訳結果:</h2>
      <div id="outputText" class="output-box"></div>
    </section>
  </main>
  <footer>
    <p>&copy; 2025 Toolbox</p>
  </footer>
  <script src="../js/translator.js"></script>
</body>
</html>
```
## translator.js
```js
// translator.js - 簡易翻訳スクリプト

document.addEventListener("DOMContentLoaded", () => {
  const inputText = document.getElementById("inputText");
  const outputText = document.getElementById("outputText");
  const translateBtn = document.getElementById("translateBtn");
  const langFrom = document.getElementById("langFrom");
  const langTo = document.getElementById("langTo");

  // 簡易辞書データ（サンプル用）
  const dictionary = {
    "ja": {
      "こんにちは": "Hello",
      "ありがとう": "Thank you",
      "さようなら": "Goodbye",
      "犬": "Dog",
      "猫": "Cat"
    },
    "en": {
      "hello": "こんにちは",
      "thank you": "ありがとう",
      "goodbye": "さようなら",
      "dog": "犬",
      "cat": "猫"
    }
  };

  // 翻訳ボタン処理
  translateBtn.addEventListener("click", () => {
    const text = inputText.value.trim();
    const from = langFrom.value;
    const to = langTo.value;

    if (!text) {
      outputText.textContent = "テキストを入力してください。";
      return;
    }

    if (from === to) {
      outputText.textContent = text;
      return;
    }

    // サンプル翻訳処理（完全一致のみ）
    const key = text.toLowerCase();
    const result = dictionary[from]?.[key] || "（辞書に見つかりませんでした）";

    outputText.textContent = result;
  });
});
```

## まとめ
- translator.html：テキスト入力欄、翻訳先・翻訳元の選択、結果表示欄を用意
- translator.js：サンプル辞書を利用した簡易的な翻訳を実装
- 実運用する場合はAPI連携を行い、入力に応じて非同期で翻訳結果を取得する仕組みに拡張可能

この形をベースにすれば、今後Google Translate APIやDeepL APIに置き換えて発展させることも容易です。

---


※この記事はChatGPTの回答を基に作成しています。