[[P08 翻訳ページ（translator.html & translator.js）のコード例]]

ジャンル・カテゴリ：ハウツー

## 目次
[[#はじめに]]  
[[#counterhtml]]  
[[#counterjs]]  
[[#まとめ]]  

## はじめに
ここでは、ツール集の一つである **文字数カウントページ** を実装するための `counter.html` と `counter.js` のサンプルコードを紹介します。入力したテキストの **文字数** と **単語数（英語などスペース区切り）** をリアルタイムで表示できるようにしています。

## counter.html
```html
<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>文字数カウント - Toolbox</title>
  <link rel="stylesheet" href="../css/style.css">
  <link rel="stylesheet" href="../css/tools.css">
</head>
<body>
  <header>
    <h1>文字数カウント</h1>
    <nav>
      <a href="../index.html">← トップへ戻る</a>
    </nav>
  </header>
  <main>
    <section class="counter">
      <label for="inputText">テキストを入力してください:</label>
      <textarea id="inputText" rows="8" placeholder="ここに文章を入力してください"></textarea>
      <div class="result-box">
        <p>文字数: <span id="charCount">0</span></p>
        <p>単語数: <span id="wordCount">0</span></p>
      </div>
    </section>
  </main>
  <footer>
    <p>&copy; 2025 Toolbox</p>
  </footer>
  <script src="../js/counter.js"></script>
</body>
</html>
```


```js
// counter.js - 文字数カウントスクリプト

document.addEventListener("DOMContentLoaded", () => {
  const inputText = document.getElementById("inputText");
  const charCount = document.getElementById("charCount");
  const wordCount = document.getElementById("wordCount");

  const updateCounts = () => {
    const text = inputText.value;

    // 文字数カウント（改行含む）
    charCount.textContent = text.length;

    // 単語数カウント（スペース区切り）
    const words = text.trim().split(/\s+/).filter(Boolean);
    wordCount.textContent = text.trim() ? words.length : 0;
  };

  // 入力ごとに更新
  inputText.addEventListener("input", updateCounts);
});
```

まとめ
- counter.html：テキストエリアと結果表示用の要素を用意
- counter.js：入力イベントに応じて文字数と単語数をリアルタイム計算
- シンプルな作りなので、日本語文章の文字数確認や英語の単語数カウントに活用可能

この形をベースにすれば、文字数の上限チェックや改行数カウントなど、機能を追加するのも容易です。

---

※この記事はChatGPTの回答を基に作成しています。

[[P10トークン数カウントページ（token-counter.html & token-counter.js）のコード例]]