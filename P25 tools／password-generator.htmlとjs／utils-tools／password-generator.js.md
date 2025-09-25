[[P24 tools／timer-and-stopwatch.htmlとjs／utils-tools／timer-and-stopwatch.js]]

**パスワード生成ツール**

---

## 機能仕様

- 生成するパスワードの **長さを指定**できる
    
- **大文字 / 小文字 / 数字 / 記号** の使用をチェックボックスで選択
    
- 「生成する」ボタンでランダムなパスワードを作成
    
- 「コピー」ボタンでクリップボードにコピー
    

---

## `tools/password-generator.html`

```html
<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8">
  <title>パスワードジェネレーター</title>
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
    #passwordOutput {
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
      <h1>パスワードジェネレーター</h1>

      <label>
        長さ: <input type="number" id="length" min="4" max="64" value="12">
      </label>

      <div class="options">
        <label><input type="checkbox" id="includeUppercase" checked> 大文字 (A-Z)</label>
        <label><input type="checkbox" id="includeLowercase" checked> 小文字 (a-z)</label>
        <label><input type="checkbox" id="includeNumbers" checked> 数字 (0-9)</label>
        <label><input type="checkbox" id="includeSymbols"> 記号 (!@#$%)</label>
      </div>

      <input type="text" id="passwordOutput" readonly>

      <button id="generateBtn">生成する</button>
      <button id="copyBtn">コピー</button>
    </section>
  </main>

  <script type="module" src="../js/utils-tools/password-generator.js"></script>
</body>
</html>
```

---

## `js/utils-tools/password-generator.js`

```js
// js/utils-tools/password-generator.js

const lengthInput = document.getElementById("length");
const includeUppercase = document.getElementById("includeUppercase");
const includeLowercase = document.getElementById("includeLowercase");
const includeNumbers = document.getElementById("includeNumbers");
const includeSymbols = document.getElementById("includeSymbols");
const passwordOutput = document.getElementById("passwordOutput");
const generateBtn = document.getElementById("generateBtn");
const copyBtn = document.getElementById("copyBtn");

// 文字セット
const UPPERCASE = "ABCDEFGHIJKLMNOPQRSTUVWXYZ";
const LOWERCASE = "abcdefghijklmnopqrstuvwxyz";
const NUMBERS = "0123456789";
const SYMBOLS = "!@#$%^&*()_+[]{}|;:,.<>?";

function generatePassword() {
  let chars = "";
  if (includeUppercase.checked) chars += UPPERCASE;
  if (includeLowercase.checked) chars += LOWERCASE;
  if (includeNumbers.checked) chars += NUMBERS;
  if (includeSymbols.checked) chars += SYMBOLS;

  const length = parseInt(lengthInput.value, 10);

  if (chars.length === 0) {
    alert("少なくとも1つの文字種を選択してください。");
    return "";
  }

  let password = "";
  for (let i = 0; i < length; i++) {
    const randomIndex = Math.floor(Math.random() * chars.length);
    password += chars[randomIndex];
  }
  return password;
}

// 生成ボタン
generateBtn.addEventListener("click", () => {
  const password = generatePassword();
  passwordOutput.value = password;
});

// コピー機能
copyBtn.addEventListener("click", () => {
  if (passwordOutput.value) {
    navigator.clipboard.writeText(passwordOutput.value)
      .then(() => alert("コピーしました！"))
      .catch(() => alert("コピーに失敗しました"));
  } else {
    alert("まずパスワードを生成してください。");
  }
});
```

---

✅ これで

- 長さ指定
    
- 使用する文字種を選択
    
- 「生成」で即ランダムパスワード
    
- 「コピー」でクリップボードへ保存
    

が可能になります。

---

👉 質問です！  
このパスワードジェネレーター、**「生成したパスワードの強度（強い / 普通 / 弱い）」を自動判定する機能**も追加しましょうか？

[[P26 tools／random-text.htmlとjs／text／random-text.js]]
