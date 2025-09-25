[[P21 日付計算ツールのコード例]]

`tools/color-name.html`（UI部分）と `js/dev-design/color-name.js`（ロジック部分）のサンプルコードを書きますね。

このツールは以下の機能を想定しています：

- HEXカラーコードまたは色名を入力すると、その色をプレビュー。
    
- HEX ⇔ RGB ⇔ HSL の相互変換を表示。
    
- 名前のある色なら、ブラウザが認識できるか確認。
    

---

### `tools/color-name.html`

```html
<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8">
  <title>カラーコード変換ツール</title>
  <link rel="stylesheet" href="../css/style.css">
</head>
<body>
  <main>
    <section class="tool-container">
      <h1>カラーコード変換ツール</h1>

      <label for="colorInput">色名またはHEXコードを入力:</label>
      <input type="text" id="colorInput" placeholder="例: red または #ff0000">

      <button id="convertBtn">変換する</button>

      <div id="colorPreview" class="color-preview"></div>

      <div id="result">
        <p><strong>HEX:</strong> <span id="hexValue">-</span></p>
        <p><strong>RGB:</strong> <span id="rgbValue">-</span></p>
        <p><strong>HSL:</strong> <span id="hslValue">-</span></p>
      </div>
    </section>
  </main>

  <script type="module" src="../js/dev-design/color-name.js"></script>
</body>
</html>
```

---

### `js/dev-design/color-name.js`

```js
// js/dev-design/color-name.js

// DOM要素取得
const colorInput = document.getElementById("colorInput");
const convertBtn = document.getElementById("convertBtn");
const colorPreview = document.getElementById("colorPreview");
const hexValue = document.getElementById("hexValue");
const rgbValue = document.getElementById("rgbValue");
const hslValue = document.getElementById("hslValue");

// HEX → RGB
function hexToRgb(hex) {
  const cleanHex = hex.replace(/^#/, "");
  const bigint = parseInt(cleanHex, 16);
  return {
    r: (bigint >> 16) & 255,
    g: (bigint >> 8) & 255,
    b: bigint & 255,
  };
}

// RGB → HEX
function rgbToHex(r, g, b) {
  return (
    "#" +
    [r, g, b]
      .map((x) => x.toString(16).padStart(2, "0"))
      .join("")
      .toUpperCase()
  );
}

// RGB → HSL
function rgbToHsl(r, g, b) {
  r /= 255;
  g /= 255;
  b /= 255;

  const max = Math.max(r, g, b);
  const min = Math.min(r, g, b);
  let h, s, l;
  l = (max + min) / 2;

  if (max === min) {
    h = s = 0; // グレー
  } else {
    const d = max - min;
    s = l > 0.5 ? d / (2 - max - min) : d / (max + min);
    switch (max) {
      case r:
        h = ((g - b) / d + (g < b ? 6 : 0));
        break;
      case g:
        h = ((b - r) / d + 2);
        break;
      case b:
        h = ((r - g) / d + 4);
        break;
    }
    h /= 6;
  }

  return {
    h: Math.round(h * 360),
    s: Math.round(s * 100),
    l: Math.round(l * 100),
  };
}

// 入力処理
function processColor() {
  let input = colorInput.value.trim();

  if (!input) return;

  // ブラウザで色を解釈できるかチェック
  const testEl = document.createElement("div");
  testEl.style.color = input;
  if (testEl.style.color === "") {
    alert("無効な色名またはコードです");
    return;
  }

  // 変換用にcanvasで正規化
  const ctx = document.createElement("canvas").getContext("2d");
  ctx.fillStyle = input;
  const computedColor = ctx.fillStyle; // 正規化された形式 (例: rgb(255,0,0))

  // RGB抽出
  const rgbMatch = computedColor.match(
    /^rgb\((\d+),\s*(\d+),\s*(\d+)\)$/
  );
  let r, g, b;
  if (rgbMatch) {
    r = parseInt(rgbMatch[1], 10);
    g = parseInt(rgbMatch[2], 10);
    b = parseInt(rgbMatch[3], 10);
  }

  // HEXに変換
  const hex = rgbToHex(r, g, b);

  // HSLに変換
  const { h, s, l } = rgbToHsl(r, g, b);

  // プレビュー
  colorPreview.style.backgroundColor = hex;

  // 結果表示
  hexValue.textContent = hex;
  rgbValue.textContent = `rgb(${r}, ${g}, ${b})`;
  hslValue.textContent = `hsl(${h}, ${s}%, ${l}%)`;
}

// イベントリスナー
convertBtn.addEventListener("click", processColor);
```

---

✅ この構成で、

- `red` や `blue` のような色名
    
- `#ff0000` のようなHEXコード
    

を入力すると、その色のプレビューとHEX / RGB / HSLが表示されます。

---

ご希望に合わせて **色名リストを表示**したり、**コピー機能**を追加することも可能ですが、まずはこの基本版を仕上げました。

👉 質問ですが、このツールは **「入力した色をプレビュー & 各形式で表示する」** だけで十分ですか？  
それとも **プリセット色の一覧（色名付きパレット）** もつけたいですか？

[[P23 tools／markdown-preview.htmlとjs／utils-tools／markdown-preview.js]]
