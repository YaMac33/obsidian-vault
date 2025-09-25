[[P16 js／clipboard.js と js／validators.js のコード例]]

ここでは、**計算系ツール**に含まれる **BMI計算機** と **割り勘計算** のための  
JavaScriptコードを紹介します。どちらもシンプルで軽量、かつバリデーションを備えています。

---

## 📄 calculators/bmi.js

```js
// ================================
// bmi.js : BMI計算スクリプト
// ================================

import { isPositive, isNumber } from "../validators.js";

/**
 * BMIを計算する
 * @param {number} weight 体重 (kg)
 * @param {number} height 身長 (cm)
 * @returns {number|null} BMI値（小数点1桁） or null
 */
export function calculateBMI(weight, height) {
  if (!isNumber(weight) || !isNumber(height) || !isPositive(weight) || !isPositive(height)) {
    return null;
  }
  const h = height / 100; // cm → m
  const bmi = weight / (h * h);
  return Math.round(bmi * 10) / 10;
}

/**
 * BMIの分類を返す
 * @param {number} bmi BMI値
 * @returns {string}
 */
export function getBMICategory(bmi) {
  if (bmi < 18.5) return "低体重 (痩せ型)";
  if (bmi < 25) return "普通体重";
  if (bmi < 30) return "肥満 (1度)";
  if (bmi < 35) return "肥満 (2度)";
  if (bmi < 40) return "肥満 (3度)";
  return "肥満 (4度)";
}

// DOM操作部分（ツールページに埋め込む想定）
document.addEventListener("DOMContentLoaded", () => {
  const weightInput = document.getElementById("weight");
  const heightInput = document.getElementById("height");
  const resultBox = document.getElementById("bmiResult");

  document.getElementById("bmiCalcBtn").addEventListener("click", () => {
    const weight = parseFloat(weightInput.value);
    const height = parseFloat(heightInput.value);
    const bmi = calculateBMI(weight, height);

    if (bmi === null) {
      resultBox.textContent = "入力値が正しくありません";
      return;
    }
    resultBox.textContent = `あなたのBMI: ${bmi} (${getBMICategory(bmi)})`;
  });
});
```

## 📄 calculators/split-bill.js
```js
// ================================
// split-bill.js : 割り勘計算スクリプト
// ================================

import { isPositive, isInteger } from "../js/validators.js";

/**
 * 割り勘金額を計算する
 * @param {number} totalAmount 合計金額
 * @param {number} people 人数
 * @returns {object|null} { perPerson, remainder } or null
 */
export function calculateSplit(totalAmount, people) {
  if (!isPositive(totalAmount) || !isInteger(people) || people <= 0) {
    return null;
  }
  const perPerson = Math.floor(totalAmount / people);
  const remainder = totalAmount % people;
  return { perPerson, remainder };
}

// DOM操作部分（ツールページに埋め込む想定）
document.addEventListener("DOMContentLoaded", () => {
  const totalInput = document.getElementById("totalAmount");
  const peopleInput = document.getElementById("people");
  const resultBox = document.getElementById("splitResult");

  document.getElementById("splitCalcBtn").addEventListener("click", () => {
    const total = parseInt(totalInput.value, 10);
    const people = parseInt(peopleInput.value, 10);
    const result = calculateSplit(total, people);

    if (!result) {
      resultBox.textContent = "入力値が正しくありません";
      return;
    }

    let msg = `1人あたり: ${result.perPerson}円`;
    if (result.remainder > 0) {
      msg += `（余り: ${result.remainder}円）`;
    }
    resultBox.textContent = msg;
  });
});
```

✅ 補足
- bmi.js  
- calculateBMI() でBMI計算
- getBMICategory() で判定を返す
- DOM操作で即時結果を表示可能

- split-bill.js  
- 合計金額 ÷ 人数で1人あたり金額を算出
- 割り切れない場合は余りを表示

どちらも validators.js を利用して入力チェックをしているため、エラーを最小限にできます。

---

※この記事はChatGPTの回答を基に作成しています。

[[P18 calculators／bmi.html と calculators／split-bill.html]]
