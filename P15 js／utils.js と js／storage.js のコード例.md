[[P14 サイト構成ツリー（便利ツール群をすべて含む）]]


ここでは、便利ツール群で共通して使える **ユーティリティ関数集 (utils.js)** と  
**ローカルストレージ操作用 (storage.js)** のサンプルコードを示します。  
軽量化を意識し、外部ライブラリは使わずに実装しています。

---

## 📄 js/utils.js

```js
// ================================
// utils.js : 共通ユーティリティ関数
// ================================

/**
 * 指定範囲のランダム整数を返す
 * @param {number} min 最小値
 * @param {number} max 最大値
 * @returns {number} min～max のランダム整数
 */
export function getRandomInt(min, max) {
  return Math.floor(Math.random() * (max - min + 1)) + min;
}

/**
 * 指定範囲の数値をクランプ（制限）する
 * @param {number} value 入力値
 * @param {number} min 最小値
 * @param {number} max 最大値
 * @returns {number} 制限後の値
 */
export function clamp(value, min, max) {
  return Math.min(Math.max(value, min), max);
}

/**
 * 数値を小数点以下指定桁数で丸める
 * @param {number} value 入力値
 * @param {number} digits 小数点以下の桁数
 * @returns {number} 丸められた数値
 */
export function roundTo(value, digits = 2) {
  const factor = Math.pow(10, digits);
  return Math.round(value * factor) / factor;
}

/**
 * HTML要素の内容をクリップボードにコピーする
 * @param {string|HTMLElement} target 対象要素のIDまたは要素本体
 * @returns {Promise<void>}
 */
export async function copyToClipboard(target) {
  let text = "";
  if (typeof target === "string") {
    const el = document.getElementById(target);
    if (!el) throw new Error(`Element with id '${target}' not found`);
    text = el.value || el.innerText || "";
  } else if (target instanceof HTMLElement) {
    text = target.value || target.innerText || "";
  }
  if (!text) return;

  try {
    await navigator.clipboard.writeText(text);
    alert("コピーしました！");
  } catch (err) {
    console.error("コピーに失敗:", err);
    alert("コピーに失敗しました");
  }
}

/**
 * 日付を YYYY-MM-DD 形式にフォーマット
 * @param {Date} date
 * @returns {string}
 */
export function formatDate(date) {
  if (!(date instanceof Date)) date = new Date(date);
  const y = date.getFullYear();
  const m = String(date.getMonth() + 1).padStart(2, "0");
  const d = String(date.getDate()).padStart(2, "0");
  return `${y}-${m}-${d}`;
}

/**
 * 簡易デバウンス関数
 * @param {Function} func 実行関数
 * @param {number} delay 遅延(ms)
 * @returns {Function} デバウンスされた関数
 */
export function debounce(func, delay = 300) {
  let timer;
  return function (...args) {
    clearTimeout(timer);
    timer = setTimeout(() => func.apply(this, args), delay);
  };
}

/**
 * 簡易スロットル関数
 * @param {Function} func 実行関数
 * @param {number} limit インターバル(ms)
 * @returns {Function} スロットルされた関数
 */
export function throttle(func, limit = 300) {
  let inThrottle;
  return function (...args) {
    if (!inThrottle) {
      func.apply(this, args);
      inThrottle = true;
      setTimeout(() => (inThrottle = false), limit);
    }
  };
}
```

## 📄 js/storage.js
```js
// ================================
// storage.js : ローカルストレージ管理
// ================================

const PREFIX = "toolbox_";

/**
 * ローカルストレージに保存
 * @param {string} key キー
 * @param {any} value 保存する値
 */
export function saveData(key, value) {
  try {
    localStorage.setItem(PREFIX + key, JSON.stringify(value));
  } catch (err) {
    console.error("保存エラー:", err);
  }
}

/**
 * ローカルストレージから取得
 * @param {string} key キー
 * @param {any} defaultValue データが無い場合のデフォルト値
 * @returns {any} 取得データ
 */
export function loadData(key, defaultValue = null) {
  try {
    const data = localStorage.getItem(PREFIX + key);
    return data ? JSON.parse(data) : defaultValue;
  } catch (err) {
    console.error("読み込みエラー:", err);
    return defaultValue;
  }
}

/**
 * ローカルストレージからデータ削除
 * @param {string} key キー
 */
export function removeData(key) {
  try {
    localStorage.removeItem(PREFIX + key);
  } catch (err) {
    console.error("削除エラー:", err);
  }
}

/**
 * 全ての保存データをクリア（toolbox関連のみ）
 */
export function clearAll() {
  try {
    Object.keys(localStorage)
      .filter((k) => k.startsWith(PREFIX))
      .forEach((k) => localStorage.removeItem(k));
  } catch (err) {
    console.error("全削除エラー:", err);
  }
}
```

## ✅ 補足
- utils.js → どのツールでも使える便利関数集。
- storage.js → 例えば「直前の入力内容を保持する」「ユーザーの設定を保存する」などに活用可能。
- 共通利用のため export 形式 を使用しています。HTMLから読み込む場合はscript type="module"を利用してください。

---

※この記事はChatGPTの回答を基に作成しています。

[[P16 js／clipboard.js と js／validators.js のコード例]]
