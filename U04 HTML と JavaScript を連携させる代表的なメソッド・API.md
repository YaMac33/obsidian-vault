[[U03 const とは？]]

HTML と JS の連携を支える API は山ほどあります。ここではカテゴリ別に**代表的でよく使うメソッド／プロパティ／API**を列挙し、用途・注意点・簡単なコード例を付けてできるだけ詳しくまとめます。実務でよく出てくるものはほぼ押さえてあるはずです — 必要なら個別に深掘りしますね。

---

## 目次（ざっくり）

1. 要素の取得（選択）
    
2. 内容・属性の読み書き
    
3. ノードの作成・挿入・削除
    
4. イベント関連（登録・制御）
    
5. フォーム操作とファイルアップロード
    
6. スタイル・レイアウト操作
    
7. スクロール・レイアウト計測
    
8. ネットワーク（fetch 等）
    
9. ストレージ（localStorage/IndexedDB 等）
    
10. 監視（MutationObserver / IntersectionObserver / ResizeObserver）
    
11. アニメーション・パフォーマンス関連
    
12. カスタムイベント / dispatch
    
13. メディア・デバイス関連
    
14. ドラッグ＆ドロップ
    
15. Web Components / Shadow DOM
    
16. 履歴・ナビゲーション（History API）
    
17. よくある注意点・ベストプラクティス
    

---

## 1) 要素の取得（選択）

- `document.getElementById(id)`
    
    - 最も速く単純。ID がユニークであることが前提。存在しなければ `null` を返す。
        
    
    ```js
    const el = document.getElementById('myId');
    ```
    
- `document.querySelector(selector)`
    
    - CSS セレクタで最初にマッチする要素を返す（`#id`, `.class`, `div > a` など）。
        
    
    ```js
    const firstLink = document.querySelector('.nav a');
    ```
    
- `document.querySelectorAll(selector)`
    
    - CSS セレクタにマッチする**静的な** NodeList を返す（forEach可能）。`for...of` や `Array.from()` で配列化可。
        
    
    ```js
    const items = document.querySelectorAll('.item');
    items.forEach(i => console.log(i));
    ```
    
- `document.getElementsByClassName(className)` / `document.getElementsByTagName(tagName)` / `document.getElementsByName(name)`
    
    - いずれも **ライブな** HTMLCollection を返す（DOM 変更で自動更新される）。古い API だが速いケースあり。
        
    
    ```js
    const list = document.getElementsByClassName('card'); // ライブ
    ```
    
- 注意: `querySelectorAll` は静的（ライブでない）／ `getElementsByClassName` はライブ、という違いがバグの原因になることがあります。
    

---

## 2) 内容・属性の読み書き

- `el.textContent`
    
    - 要素のテキストを取得／設定（HTML を無視）。安全にテキストを入れたいときに使う。
        
- `el.innerText`
    
    - レイアウトに基づくテキスト（レンダリングに遅延する場合あり）。多言語やレイアウト調査時に差が出ることがある。
        
- `el.innerHTML`
    
    - 要素の HTML を文字列として読み書き（注：XSS に注意）。
        
    
    ```js
    el.innerHTML = '<strong>強調</strong>';
    ```
    
- 属性操作:
    
    - `el.getAttribute(name)`, `el.setAttribute(name, value)`, `el.removeAttribute(name)`, `el.hasAttribute(name)`
        
- フォーム系値:
    
    - `input.value`（テキスト等）、`select.value`、`checkbox.checked`、`fileInput.files`（FileList）
        
- `dataset`（data-* 属性）:
    
    ```html
    <div id="x" data-user-id="42"></div>
    ```
    
    ```js
    const d = document.getElementById('x').dataset.userId; // "42"
    ```
    
- `classList`（クラス操作）:
    
    - `el.classList.add('a')`, `.remove()`, `.toggle()`, `.contains()`, `.replace()`
        
    
    ```js
    el.classList.toggle('is-active');
    ```
    

---

## 3) ノードの作成・挿入・削除

- `document.createElement(tagName)`, `document.createTextNode(text)`
    
- 挿入系:
    
    - `parent.appendChild(node)`（古典）
        
    - `parent.append(...nodesOrStrings)`（複数・文字列可、最新）
        
    - `parent.prepend(...)`
        
    - `referenceNode.insertAdjacentHTML(position, html)` — `beforebegin/afterbegin/beforeend/afterend`
        
    - `parent.insertBefore(newNode, referenceNode)`
        
- 置換／削除:
    
    - `node.replaceWith(newNode)`, `parent.removeChild(node)`, `node.remove()`（モダン）
        
- `node.cloneNode(deep)`（深いコピー）
    
- `document.createDocumentFragment()`（多数の要素を一時的にまとめて挿入するときに高速）
    

例:

```js
const card = document.createElement('div');
card.className = 'card';
card.textContent = 'Hello';
document.body.appendChild(card);
```

---

## 4) イベント関連（登録・制御）

- `element.addEventListener(type, listener, options)`
    
    - options: `{capture: boolean, once: boolean, passive: boolean}`
        
    - `once: true` で一回だけ自動解除、`passive: true` は `preventDefault` を使わないスクロール系イベントに最適（パフォーマンス向上）。
        
- `element.removeEventListener(type, listener, options)`
    
- イベントオブジェクト（`event`）の代表的プロパティ:
    
    - `event.target`（発生元）、`event.currentTarget`（リスナー登録先）、`event.type`、`event.preventDefault()`, `event.stopPropagation()`, `event.stopImmediatePropagation()`
        
- **イベントデリゲーション**（親要素に一つだけ listener を置いて子のイベントを扱う） — パフォーマンス良し・動的要素にも対応。
    
    ```js
    parent.addEventListener('click', e => {
      const button = e.target.closest('button');
      if (!button) return;
      // button に対する処理
    });
    ```
    
- DOM の準備:
    
    - `document.addEventListener('DOMContentLoaded', () => { ... })` — DOM 構築完了後に実行。
        
    - `<script defer src="...">` を使えば DOMContentLoaded 前にスクリプト読み込みが遅延される。
        

---

## 5) フォーム操作とファイルアップロード

- `form.elements`（フォーム内の要素群）、`form.submit()`（プログラムで送信）
    
- `input.checkValidity()`, `input.reportValidity()`, `input.setCustomValidity(message)`（バリデーション）
    
- `new FormData(form)` や `const fd = new FormData(); fd.append('file', fileInput.files[0]);`
    
    - `fetch('/upload', { method: 'POST', body: fd })` の組合せでファイル送信。
        
- `input.files` (`FileList`) と `File` オブジェクト（`size`, `type`, `name` など）
    

---

## 6) スタイル・CSS 操作

- `el.style`（インラインスタイル。プロパティ名はキャメルケース）
    
    ```js
    el.style.backgroundColor = 'red';
    ```
    
- `getComputedStyle(el)`（実際の計算済みスタイルを取得）
    
- `el.classList`（前述。クラスでスタイルを切り替えるのが推奨）
    
- CSS カスタムプロパティ操作:
    
    ```js
    el.style.setProperty('--main', '#f00');
    const v = getComputedStyle(el).getPropertyValue('--main');
    ```
    

---

## 7) スクロール・レイアウト計測

- `el.getBoundingClientRect()`（位置・幅・高さ・top/left を取得）
    
- `el.offsetWidth/offsetHeight`, `el.clientWidth/clientHeight`, `el.scrollWidth/scrollHeight`
    
- スクロール位置: `el.scrollTop`, `document.documentElement.scrollTop`
    
- スクロール移動: `el.scrollIntoView({behavior: 'smooth'})`, `window.scrollTo(x,y)` / `scrollBy`
    
- `offsetParent`, `offsetTop`, `offsetLeft`（レイアウト追跡に便利）
    

---

## 8) ネットワーク（fetch 等）

- `fetch(url, options)`（Promise ベース） — 推奨。JSON 送受信やフォーム送信に使う。
    
    ```js
    const res = await fetch('/api/data');
    const data = await res.json();
    ```
    
- `XMLHttpRequest`（古いが互換性用）
    
- `WebSocket`（双方向通信）
    
- `EventSource`（Server-Sent Events）
    

---

## 9) ストレージ

- `localStorage.setItem('k','v')` / `localStorage.getItem('k')`（永続、同期 API）
    
- `sessionStorage`（タブ / セッション単位）
    
- `IndexedDB`（大量データや構造化データ。非同期で高機能）
    
- `document.cookie`（古い。使用時はセキュリティに注意）
    

---

## 10) 監視 API（DOM の変化や可視化）

- `MutationObserver`
    
    - DOM の追加／削除／属性変化を監視する。`MutationObserver` を使うと効率的に反応できる（`setInterval` より推奨）。
        
    
    ```js
    const obs = new MutationObserver(mutations => { /* ... */ });
    obs.observe(targetNode, { childList: true, subtree: true, attributes: true });
    ```
    
- `IntersectionObserver`（要素がビューポートに入ったかを監視／遅延読み込み・無限スクロールに便利）
    
    ```js
    const io = new IntersectionObserver(entries => {
      entries.forEach(e => { if (e.isIntersecting) loadImage(e.target); });
    });
    io.observe(img);
    ```
    
- `ResizeObserver`（要素のサイズ変化を監視）
    

---

## 11) アニメーション・パフォーマンス関連

- `requestAnimationFrame(callback)`（スムーズな描画ループ）
    
- `element.animate(keyframes, options)`（Web Animations API） — 高度なアニメーション制御が可能
    
- `setTimeout` / `setInterval`（簡易タイマー）
    
- `performance.now()`（高精度タイムスタンプ）
    

---

## 12) カスタムイベント / dispatch

- `new CustomEvent('myEvent', { detail: { foo: 1 }})`
    
- `el.dispatchEvent(event)` で任意のイベントを発火し、他のコードに通知できる。
    
    ```js
    const ev = new CustomEvent('user:login', { detail: { id: 5 }});
    document.dispatchEvent(ev);
    ```
    

---

## 13) メディア・デバイス関連

- `navigator.mediaDevices.getUserMedia({ video: true, audio: true })`（カメラ／マイク取得）
    
- `<video>` / `<audio>` 要素の API（play, pause, currentTime, volume）
    
- Canvas API (`canvas.getContext('2d')`)／WebGL（3D）
    
- `navigator.clipboard.readText()` / `writeText()`（クリップボード操作） — HTTPS 必須
    
- Geolocation API: `navigator.geolocation.getCurrentPosition(...)`（ユーザー許可が必要）
    

---

## 14) ドラッグ & ドロップ

- イベント: `dragstart`, `dragover`, `drop`, `dragend`
    
- `event.dataTransfer` にデータを入れたりファイルを受け取ったりする。
    
    ```js
    el.addEventListener('drop', e => {
      e.preventDefault();
      const files = e.dataTransfer.files;
    });
    ```
    

---

## 15) Web Components / Shadow DOM（モダン）

- `class MyEl extends HTMLElement { ... }`
    
- `customElements.define('my-el', MyEl)`
    
- Shadow DOM: `this.attachShadow({mode: 'open'})` でスタイルのカプセル化
    
- `<template>` 要素と `content` を使ってテンプレートを再利用
    

これにより**再利用可能でカプセル化されたコンポーネント**が作れます。

---

## 16) 履歴・ナビゲーション（History API）

- `history.pushState(stateObj, title, url)` / `history.replaceState(...)`
    
- `window.addEventListener('popstate', e => {})` で戻る・進むに対応できる（SPAで必須）。
    

---

## 17) よくある注意点・ベストプラクティス

- **DOM が準備されてから操作**する（`DOMContentLoaded` / `defer` / `<script>` を `</body>` の直前）。
    
- **イベントリスナーは不要になったら解除**する（メモリリーク予防）。
    
- `innerHTML` にユーザー入力を直接入れるのは **XSS の危険あり** → `textContent` を使うかサニタイズする。
    
- スクロールや `scroll` / `touch` イベントには `passive: true` を検討（パフォーマンス改善）。
    
    ```js
    window.addEventListener('touchstart', onTouch, { passive: true });
    ```
    
- 大量 DOM 更新は `DocumentFragment` を使うか、仮想 DOM（フレームワーク）を検討。
    
- ブラウザ互換性：`IntersectionObserver`, `ResizeObserver`, Web Animations などは古いブラウザで未対応の場合がある → polyfill を検討。
    

---

## 具体的なコード例（いくつか重要パターン）

### イベントデリゲーション（クリック処理を親でまとめる）

```html
<ul id="list">
  <li data-id="1">A</li>
  <li data-id="2">B</li>
</ul>
<script>
const list = document.getElementById('list');
list.addEventListener('click', (e) => {
  const li = e.target.closest('li');
  if (!li || !list.contains(li)) return;
  console.log('clicked id=', li.dataset.id);
});
</script>
```

### IntersectionObserver で画像の遅延読み込み

```js
const io = new IntersectionObserver((entries) => {
  entries.forEach(entry => {
    if (!entry.isIntersecting) return;
    const img = entry.target;
    img.src = img.dataset.src;
    io.unobserve(img);
  });
});
document.querySelectorAll('img[data-src]').forEach(img => io.observe(img));
```

### MutationObserver（DOM追加を監視）

```js
const mo = new MutationObserver(mutations => {
  for (const m of mutations) {
    if (m.addedNodes.length) console.log('node added');
  }
});
mo.observe(document.body, { childList: true, subtree: true });
```

### カスタムイベントの発火と受信

```js
// 発火側
document.dispatchEvent(new CustomEvent('myapp:ready', { detail: { ts: Date.now() }}));

// 受信側
document.addEventListener('myapp:ready', e => {
  console.log(e.detail.ts);
});
```

---

## 最後に — 使い分けの簡単な目安

- 単純な要素取得 → `getElementById`（ID が分かっているなら）か `querySelector`
    
- 動的に増える要素のイベント → **イベントデリゲーション**
    
- 多数の DOM 追加 → `DocumentFragment` or `innerHTML`（XSS注意）
    
- スクロールや可視状態に依存する処理 → `IntersectionObserver`
    
- DOM 変更を効率的に監視 → `MutationObserver`
    
- ユーザーのメディア/カメラを使う → `getUserMedia`
    
- 永続的データ保存 → `localStorage`（簡単）／`IndexedDB`（大量・構造化）
    

---

欲しい部分があればさらにコードで解説します（例：`FormData + fetch` のアップロード実装、`WebSocket` のハンドリング、`Web Components` の実装テンプレートなど）。どのカテゴリをもっと深掘りしましょうか？