[[P17 calculators／bmi.js と calculators／split-bill.js のコード例]]

以下に **tools/bmi.html** と **tools/split-bill.html** のコード例を示します。  
それぞれ、`calculators/bmi.js` と `calculators/split-bill.js` を読み込む形になっています。

---

## tools/bmi.html

```html
<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>BMI計算機</title>
  <link rel="stylesheet" href="../css/tools.css">
</head>
<body>
  <header>
    <h1>BMI計算機</h1>
  </header>

  <main>
    <section class="tool-container">
      <label for="weight">体重 (kg):</label>
      <input type="number" id="weight" placeholder="例: 70" required>

      <label for="height">身長 (cm):</label>
      <input type="number" id="height" placeholder="例: 170" required>

      <button id="calc-bmi">計算する</button>

      <div id="bmi-result" class="result-box"></div>
    </section>
  </main>

  <footer>
    <p><a href="../index.html">← ホームに戻る</a></p>
  </footer>

  <script src="../js/utils.js"></script>
  <script src="../js/calculators/bmi.js"></script>
</body>
</html>
```

## tools/split-bill.html
```html
<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>割り勘計算機</title>
  <link rel="stylesheet" href="../css/tools.css">
</head>
<body>
  <header>
    <h1>割り勘計算機</h1>
  </header>

  <main>
    <section class="tool-container">
      <label for="total">合計金額 (円):</label>
      <input type="number" id="totalAmount" placeholder="例: 12000" required>

      <label for="people">人数:</label>
      <input type="number" id="people" placeholder="例: 4" required>

      <button id="splitCalcBtn">計算する</button>

      <div id="splitResult" class="result-box"></div>
    </section>
  </main>

  <footer>
    <p><a href="../index.html">← ホームに戻る</a></p>
  </footer>

  <script type="module" src="../js/utils.js"></script>
  <script type="module" src="../js/calculators/split-bill.js"></script>
</body>
</html>
```

## 今日(2025-09-19)のエラーの原因

- HTMLとJSでIDの不一致  
- `bmi.js` 内で  
  ```js
  import { isPositive, isNumber } from "../js/validators.js";
  ```
  を使っているので、HTML側の script に type="module" を指定しないとエラーになります。

修正前
```js
<script src="../calculators/bmi.js"></script>
```

修正後
```js
<script type="module" src="../calculators/bmi.js"></script>
```

[[P19 通貨換算ページのコード例]]
