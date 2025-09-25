[[P27 共通化]]

**任意の階層に対応できる include.js** を作ります。  
ポイントは「現在の HTML ファイルの階層に応じて header.html / footer.html のパスを自動で計算」することです。

---

## 改良版 include.js（多階層対応）

```js
// js/include.js
document.addEventListener("DOMContentLoaded", () => {

  /**
   * 現在の階層に応じて header.html / footer.html のパスを計算
   * - /index.html         → ./header.html
   * - /tools/bmi.html     → ../header.html
   * - /tools/calculator/advanced.html → ../../header.html
   */
  function getBasePath() {
    // 現在のパスを取得（例: /tools/calculator/advanced.html）
    const path = window.location.pathname;
    // HTML ファイルのある階層部分だけを抽出
    const segments = path.split("/").filter(seg => seg.length > 0);
    // ルートからの階層数（ファイル名を除く）
    const depth = segments.length > 1 ? segments.length - 1 : 0;
    // "../" を階層数分連結
    return "../".repeat(depth);
  }

  const basePath = getBasePath();

  // ヘッダー読み込み
  fetch(basePath + "header.html")
    .then(response => response.text())
    .then(data => {
      document.getElementById("header").innerHTML = data;
    })
    .catch(err => console.error("ヘッダー読み込みエラー:", err));

  // フッター読み込み
  fetch(basePath + "footer.html")
    .then(response => response.text())
    .then(data => {
      document.getElementById("footer").innerHTML = data;
    })
    .catch(err => console.error("フッター読み込みエラー:", err));
});
```

---

## 使用例

### 1. ルート直下の index.html

```html
<div id="header"></div>
<main>...</main>
<div id="footer"></div>
<script defer src="js/include.js"></script>
```

### 2. サブフォルダ内の tools/bmi.html

```html
<div id="header"></div>
<main>...</main>
<div id="footer"></div>
<script defer src="../js/include.js"></script>
```

### 3. さらに階層が深い tools/calculator/advanced.html

```html
<div id="header"></div>
<main>...</main>
<div id="footer"></div>
<script defer src="../../js/include.js"></script>
```

---

## ✅ この方法のメリット

1. **階層が深くても header.html / footer.html を自動取得**
    
2. 各ページに書く HTML はほぼ同じ（`<div id="header"></div>` と `<div id="footer"></div>` を置くだけ）
    
3. GitHub Pages でも静的サイトとして問題なく共通化可能
    

---

💡 追加アドバイス：

- `header.html` と `footer.html` の相対パスが複雑になる場合、最上位フォルダに `js/include.js` を置くと管理が楽です。
    
- CSS や JS のパスも同じ階層問題が発生するので、`<link>` や `<script>` も相対パスを階層に応じて調整してください。
    

---

整理して詳しく解説します。

---

## 1. コメント部分の意図

```js
/**
 * 現在の階層に応じて header.html / footer.html のパスを計算
 * - /index.html         → ./header.html
 * - /tools/bmi.html     → ../header.html
 * - /tools/calculator/advanced.html → ../../header.html
 */
```

- **目的**：HTML がどの階層にあるかに応じて、共通ヘッダーやフッターのファイルパスを自動で補正する
    
- これがないと、サブディレクトリにあるページから `header.html` を読み込めません
    

---

## 2. 関数の処理の流れ

```js
function getBasePath() {
  const path = window.location.pathname;
  const segments = path.split("/").filter(seg => seg.length > 0);
  const depth = segments.length > 1 ? segments.length - 1 : 0;
  return "../".repeat(depth);
}
```

### ステップごとに説明

1. **現在のページパスを取得**
    

```js
const path = window.location.pathname;
```

- 例: `/tools/calculator/advanced.html`
    

2. **パスを `/` で分割して空文字を削除**
    

```js
const segments = path.split("/").filter(seg => seg.length > 0);
```

- `/tools/calculator/advanced.html` → `["tools", "calculator", "advanced.html"]`
    

3. **HTML ファイル名を除いた階層数を計算**
    

```js
const depth = segments.length > 1 ? segments.length - 1 : 0;
```

- 最後の要素はファイル名なので、`-1` して階層数を取得
    
- 例: `["tools", "calculator", "advanced.html"]` → `depth = 2`
    

4. **`../` を階層数分繰り返して返す**
    

```js
return "../".repeat(depth);
```

- 例: `depth = 2` → `"../../"`
    
- これを `fetch(basePath + "header.html")` に使うことで、どの階層からでも正しいパスで読み込み可能
    

---

## 3. 実際の使い方

```js
const basePath = getBasePath();
fetch(basePath + "header.html")
  .then(res => res.text())
  .then(data => document.getElementById("header").innerHTML = data);
```

- これにより、どの階層のページでも共通ヘッダーが正しく読み込まれます。
    

---

## 4. 注意点

- `window.location.pathname` は **ブラウザでの絶対パス** を返す
    
    - 例: `http://example.com/tools/bmi.html` → `/tools/bmi.html`
        
- GitHub Pages やローカルでテストするときは、**サーバー環境で確認**すること
    
    - ローカルで `file://` から開くと `/` が正しく動作しない場合があります
        

---

💡 まとめ

- この `getBasePath` 関数は **階層に応じた相対パス補正** として正しい
    
- `fetch(basePath + "header.html")` で、ルート直下からサブフォルダまでどこでも読み込める設計
    

---
