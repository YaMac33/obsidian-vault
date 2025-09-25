[[P09 文字数カウントページ（counter.html & counter.js）のコード例]]

ジャンル・カテゴリ：ハウツー

## 目次
[[#はじめに]]  
[[#token-counterhtml]]  
[[#token-counterjs]]  
[[#まとめ]]  

## はじめに
ここでは、ツール集の一つである **トークン数カウントページ** を実装するための `token-counter.html` と `token-counter.js` のサンプルコードを紹介します。  
ChatGPTなどのLLMを利用する際に重要になる「トークン数」を簡易的に数える仕組みを用意しています。ここでは正確なライブラリを使わず、簡易的に「空白区切り＋句読点考慮」でカウントする例を示します。

## token-counter.html
```html
<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>トークン数カウント - Toolbox</title>
  <link rel="stylesheet" href="../css/style.css">
  <link rel="stylesheet" href="../css/tools.css">
</head>
<body>
  <header>
    <h1>トークン数カウント</h1>
    <nav>
      <a href="../index.html">← トップへ戻る</a>
    </nav>
  </header>
  <main>
    <section class="token-counter">
      <label for="inputText">テキストを入力してください:</label>
      <textarea id="inputText" rows="8" placeholder="ここに文章を入力してください"></textarea>
      <div class="result-box">
        <p>推定トークン数: <span id="tokenCount">0</span></p>
      </div>
      <p class="note">※このカウントは簡易的な目安です。正確なトークン数はtiktoken等のライブラリを利用してください。</p>
    </section>
  </main>
  <footer>
    <p>&copy; 2025 Toolbox</p>
  </footer>
  <script src="../js/token-counter.js"></script>
</body>
</html>
```

```js
// token-counter.js - 簡易トークン数カウントスクリプト

document.addEventListener("DOMContentLoaded", () => {
  const inputText = document.getElementById("inputText");
  const tokenCount = document.getElementById("tokenCount");

  const estimateTokens = (text) => {
    // 正規表現で単語や記号を分割
    const tokens = text.trim().split(/[\s、。,.!?；;:"'(){}[\]]+/).filter(Boolean);
    return tokens.length;
  };

  const updateCount = () => {
    const text = inputText.value;
    tokenCount.textContent = estimateTokens(text);
  };

  inputText.addEventListener("input", updateCount);
});
```

## まとめ
- token-counter.html：テキスト入力欄と結果表示欄を用意
- token-counter.js：簡易的にトークンを空白・句読点で分割してカウント
- 注意点として、この方法はあくまで「目安」であり、実際のLLM（ChatGPTなど）が用いるトークナイザーとは結果が異なる可能性がある

本格的に運用する場合は、[tiktoken](https://github.com/openai/tiktoken) などの公式ライブラリを組み込むことを推奨します。

---


※この記事はChatGPTの回答を基に作成しています。

[[P11 QRコード生成ページ（tools／qr-generator.html）]]