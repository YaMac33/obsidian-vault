[[P06 シンプルなWebツール集のベースコード例]]

ジャンル・カテゴリ：ハウツー

## 目次
[[#はじめに]]  
[[#calculatorhtml]]  
[[#calculatorjs]]  
[[#まとめ]]  

## はじめに
ここでは、ツール集の一つである **電卓ページ** を実装するための `calculator.html` と `calculator.js` のサンプルコードを紹介します。シンプルな四則演算に対応し、UIも分かりやすい形で構成します。

## calculator.html
```html
<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>電卓 - Toolbox</title>
  <link rel="stylesheet" href="../css/style.css">
  <link rel="stylesheet" href="../css/tools.css">
</head>
<body>
  <header>
    <h1>電卓</h1>
    <nav>
      <a href="../index.html">← トップへ戻る</a>
    </nav>
  </header>
  <main>
    <section class="calculator">
      <input type="text" id="display" disabled>
      <div class="buttons">
        <button class="btn" data-value="7">7</button>
        <button class="btn" data-value="8">8</button>
        <button class="btn" data-value="9">9</button>
        <button class="btn operator" data-value="/">÷</button>
        <button class="btn" data-value="4">4</button>
        <button class="btn" data-value="5">5</button>
        <button class="btn" data-value="6">6</button>
        <button class="btn operator" data-value="*">×</button>
        <button class="btn" data-value="1">1</button>
        <button class="btn" data-value="2">2</button>
        <button class="btn" data-value="3">3</button>
        <button class="btn operator" data-value="-">−</button>
        <button class="btn" data-value="0">0</button>
        <button class="btn" data-value=".">.</button>
        <button class="btn" id="clear">C</button>
        <button class="btn operator" data-value="+">＋</button>
        <button class="btn equal" id="equals">＝</button>
      </div>
    </section>
  </main>
  <footer>
    <p>&copy; 2025 Toolbox</p>
  </footer>
  <script src="../js/calculator.js"></script>
</body>
</html>
```

## calculator.js
```js
// calculator.js - 電卓スクリプト

document.addEventListener("DOMContentLoaded", () => {
  const display = document.getElementById("display");
  const buttons = document.querySelectorAll(".btn");
  const clearBtn = document.getElementById("clear");
  const equalsBtn = document.getElementById("equals");

  let currentInput = "";

  // ボタン入力処理
  buttons.forEach(button => {
    button.addEventListener("click", () => {
      const value = button.getAttribute("data-value");
      if (value) {
        currentInput += value;
        display.value = currentInput;
      }
    });
  });

  // クリア処理
  clearBtn.addEventListener("click", () => {
    currentInput = "";
    display.value = "";
  });

  // 計算処理
  equalsBtn.addEventListener("click", () => {
    try {
      const result = eval(currentInput); // evalは簡易実装用
      display.value = result;
      currentInput = result.toString();
    } catch (error) {
      display.value = "Error";
      currentInput = "";
    }
  });
});
```

## まとめ
- calculator.html：UIをHTMLで定義し、ボタン配置を整備
- calculator.js：入力・計算・クリア機能を実装
- eval を利用して簡易的に四則演算を実現（本格利用では安全対策が必要）

この組み合わせで、最小限動作する電卓ページが完成します。

---

※この記事はChatGPTの回答を基に作成しています。

[[P08 翻訳ページ（translator.html & translator.js）のコード例]]