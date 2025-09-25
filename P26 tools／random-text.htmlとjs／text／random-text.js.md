[[P25 tools／password-generator.htmlとjs／utils-tools／password-generator.js]]

**ランダムテキスト生成ツール**

---

## 機能仕様

- テキストの種類を選べる（例：英字・数字・記号・日本語かな）
    
- 生成する文字数を指定できる
    
- 「生成」ボタンでランダムな文字列を作成
    
- 「コピー」ボタンでクリップボードにコピー
    

---

## `tools/random-text.html`

```html
<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8">
  <title>ランダムテキストジェネレーター</title>
  <link rel="stylesheet" href="../css/style.css">
  <style>
    .tool-container {
      max-width: 500px;
      margin: auto;
      padding: 20px;
    }
    .options {
      margin: 10px 0;
    }
    label {
      display: block;
      margin: 5px 0;
    }
    #randomTextOutput {
      width: 100%;
      padding: 10px;
      font-size: 1.2em;
      margin: 10px 0;
    }
    button {
      margin: 5px;
    }
  </style>
</head>
<body>
  <main>
    <section class="tool-container">
      <h1>ランダムテキストジェネレーター</h1>

      <label>
        長さ: <input type="number" id="textLength" min="1" max="200" value="20">
      </label>

      <div class="options">
        <label><input type="checkbox" id="includeLetters" checked> 英字 (a-zA-Z)</label>
        <label><input type="checkbox" id="includeNumbers" checked> 数字 (0-9)</label>
        <label><input type="checkbox" id="includeSymbols"> 記号 (!@#$%)</label>
        <label><input type="checkbox" id="includeKana"> かな (あいうえお…)</label>
      </div>

      <textarea id="randomTextOutput" rows="4" readonly></textarea>

      <button id="generateTextBtn">生成する</button>
      <button id="copyTextBtn">コピー</button>
    </section>
  </main>

  <script type="module" src="../js/text/random-text.js"></script>
</body>
</html>
```

---

## `js/text/random-text.js`

```js
// js/text/random-text.js

const textLengthInput = document.getElementById("textLength");
const includeLetters = document.getElementById("includeLetters");
const includeNumbers = document.getElementById("includeNumbers");
const includeSymbols = document.getElementById("includeSymbols");
const includeKana = document.getElementById("includeKana");
const randomTextOutput = document.getElementById("randomTextOutput");
const generateTextBtn = document.getElementById("generateTextBtn");
const copyTextBtn = document.getElementById("copyTextBtn");

// 文字セット
const LETTERS = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz";
const NUMBERS = "0123456789";
const SYMBOLS = "!@#$%^&*()_+[]{}|;:,.<>?";
const KANA = "あいうえおかきくけこさしすせそたちつてとなにぬねのはひふへほまみむめもやゆよらりるれろわをん";

function generateRandomText() {
  let chars = "";
  if (includeLetters.checked) chars += LETTERS;
  if (includeNumbers.checked) chars += NUMBERS;
  if (includeSymbols.checked) chars += SYMBOLS;
  if (includeKana.checked) chars += KANA;

  const length = parseInt(textLengthInput.value, 10);

  if (chars.length === 0) {
    alert("少なくとも1つの文字種を選択してください。");
    return "";
  }

  let result = "";
  for (let i = 0; i < length; i++) {
    const randomIndex = Math.floor(Math.random() * chars.length);
    result += chars[randomIndex];
  }
  return result;
}

// 生成ボタン
generateTextBtn.addEventListener("click", () => {
  const text = generateRandomText();
  randomTextOutput.value = text;
});

// コピー機能
copyTextBtn.addEventListener("click", () => {
  if (randomTextOutput.value) {
    navigator.clipboard.writeText(randomTextOutput.value)
      .then(() => alert("コピーしました！"))
      .catch(() => alert("コピーに失敗しました"));
  } else {
    alert("まずテキストを生成してください。");
  }
});
```

---

✅ これで

- 英字・数字・記号・かなから選択
    
- 任意の長さを指定
    
- 「生成」でランダムテキストを表示
    
- 「コピー」でクリップボード保存
    

が可能になります。

---

👉 質問です！  
このランダムテキスト、**「文章風（例文っぽい）」生成モード** も追加した方が便利だと思いますか？

[[P27 共通化]]
