[[P15 js／utils.js と js／storage.js のコード例]]

ここでは、便利ツール群で役立つ **クリップボード操作用 (clipboard.js)** と  
入力チェックをまとめた **バリデーション関数集 (validators.js)** のサンプルコードを紹介します。  
どちらも外部ライブラリを使わずに軽量で汎用的に利用できるようにしています。

---

## 📄 js/clipboard.js

```js
// ================================
// clipboard.js : クリップボード操作
// ================================

/**
 * 任意のテキストをクリップボードにコピー
 * @param {string} text コピーするテキスト
 * @returns {Promise<void>}
 */
export async function copyText(text) {
  if (!text) {
    alert("コピーする内容がありません");
    return;
  }
  try {
    await navigator.clipboard.writeText(text);
    alert("コピーしました！");
  } catch (err) {
    console.error("コピー失敗:", err);
    alert("コピーに失敗しました");
  }
}

/**
 * 指定要素の内容をコピー
 * @param {string|HTMLElement} targetIdOrEl 対象ID または HTMLElement
 */
export async function copyElementContent(targetIdOrEl) {
  let el;
  if (typeof targetIdOrEl === "string") {
    el = document.getElementById(targetIdOrEl);
  } else {
    el = targetIdOrEl;
  }
  if (!el) {
    console.warn("対象要素が見つかりません");
    return;
  }
  const text = el.value || el.innerText || "";
  return copyText(text);
}

/**
 * クリップボードからテキストを貼り付け
 * @returns {Promise<string>} 取得したテキスト
 */
export async function pasteText() {
  try {
    const text = await navigator.clipboard.readText();
    return text;
  } catch (err) {
    console.error("貼り付け失敗:", err);
    alert("貼り付けに失敗しました");
    return "";
  }
}
```

## 📄 js/validators.js
```js
// ================================
// validators.js : 入力バリデーション関数集
// ================================

/**
 * 空文字チェック
 * @param {string} value 入力値
 * @returns {boolean}
 */
export function isNotEmpty(value) {
  return value != null && value.trim().length > 0;
}

/**
 * 数値チェック
 * @param {string|number} value 入力値
 * @returns {boolean}
 */
export function isNumber(value) {
  return !isNaN(value) && value !== "" && value !== null;
}

/**
 * 整数チェック
 * @param {string|number} value
 * @returns {boolean}
 */
export function isInteger(value) {
  return Number.isInteger(Number(value));
}

/**
 * 正の数チェック
 * @param {string|number} value
 * @returns {boolean}
 */
export function isPositive(value) {
  return isNumber(value) && Number(value) > 0;
}

/**
 * メールアドレス形式チェック
 * @param {string} email
 * @returns {boolean}
 */
export function isEmail(email) {
  const re = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
  return re.test(email);
}

/**
 * URL形式チェック
 * @param {string} url
 * @returns {boolean}
 */
export function isUrl(url) {
  try {
    new URL(url);
    return true;
  } catch {
    return false;
  }
}

/**
 * JSON文字列チェック
 * @param {string} str
 * @returns {boolean}
 */
export function isJson(str) {
  try {
    JSON.parse(str);
    return true;
  } catch {
    return false;
  }
}

/**
 * パスワード強度チェック
 * 8文字以上、大文字・小文字・数字・記号を含むか
 * @param {string} password
 * @returns {boolean}
 */
export function isStrongPassword(password) {
  const re = /^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)(?=.*[\W_]).{8,}$/;
  return re.test(password);
}
```

## ✅ 補足
- clipboard.js  
- copyText() で文字列コピー
- copyElementContent() で要素内容コピー
- pasteText() で貼り付け取得

- validators.js  
- 文字・数値・メール・URL・JSON・パスワード強度などをまとめてチェック
- どのツールにも流用可能

両方とも、モジュール形式 (export) を使っているため、HTML側で利用する場合は
<script type="module" src="..."></script> で読み込むようにしてください。

---

※この記事はChatGPTの回答を基に作成しています。

[[P17 calculators／bmi.js と calculators／split-bill.js のコード例]]