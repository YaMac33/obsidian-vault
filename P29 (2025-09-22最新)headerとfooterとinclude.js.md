[[P28 各ページ・各コンテンツのHTMLファイル内に書くコード文]]

```
<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>便利ツールポータル - Toolbox</title>
  <link rel="stylesheet" href="css/style.css">
  <link rel="stylesheet" href="css/tools.css">
  <script defer src="js/include.js"></script>
</head>
<body>
  <!-- 共通ヘッダーを読み込み -->
  <div id="header"></div>

  <!-- メインコンテンツ -->
  <main class="tool-wrapper">
  <h2>便利ツールページのアイデア</h2>

  <section>
    <h3>計算系</h3>
    <ul>
      <li><a href="tools/calculator.html">電卓（四則演算・応用計算）</a></li>
      <li><a href="tools/bmi.html">BMI計算機</a></li>
      <li><a href="tools/currency-converter.html">通貨換算・暗号資産換算</a></li>
    </ul>
  </section>

  <section>
    <h3>文字・文章系</h3>
    <ul>
      <li><a href="tools/counter.html">文字数カウント</a></li>
      <li><a href="tools/token-counter.html">トークン数カウント（AI用プロンプトチェック）</a></li>
      <li><a href="tools/random-text.html">ランダム文章生成（例：例文・テスト用）</a></li>
      <li><a href="tools/romaji.html">ローマ字変換</a></li>
    </ul>
  </section>

  <section>
    <h3>便利ツール系</h3>
    <ul>
      <li><a href="tools/qr-generator.html">QR生成</a></li>
      <li><a href="tools/date-calc.html">日付計算（○日後、○日前）</a></li>
      <li><a href="tools/timer-and-stopwatch.html">タイマー・ストップウォッチ</a></li>
      <li><a href="tools\color-name.html">色コード確認・カラーサンプル</a></li>
      <li><a href="tools\markdown-preview.html">Markdownプレビュー</a></li>
      <li><a href="tools\password-generator.html">パスワード生成</a></li>
    </ul>
  </section>

  <section>
    <h3>開発・デザイン系</h3>
    <ul>
      <li><a href="https://codepen.io/pen/">HTML/CSS/JavaScriptライブ編集</a></li>
      <li><a href="tools\color-name.html">色名からカラーコード変換</a></li>
    </ul>
  </section>
</main>

  <!-- 共通フッターを読み込み -->
  <div id="footer"></div>
</body>
</html>
```


```
<!-- header.html -->
<script async src="https://www.googletagmanager.com/gtag/js?id=G-MQ9ZB4LYWP"></script>
<script>
    window.dataLayer = window.dataLayer || [];
    function gtag(){dataLayer.push(arguments);}
    gtag('js', new Date());
    gtag('config', 'G-MQ9ZB4LYWP');
</script>

<header class="site-header">
    <div class="header-container">
        <h1><a href="/toolbox/index.html" class="logo">
            <span class="logo-tool">Tool</span><span class="logo-box">box</span>
        </a></h1>
    </div>
</header>
```

```
<!-- footer.html -->
<footer class="site-footer">
  <div class="container">
    <p>&copy; 2025 Toolbox</p>
  </div>
</footer>
```

```
// js/include.js
document.addEventListener("DOMContentLoaded", () => {
  const basePath = location.pathname.includes("/tools/") ? "../" : "./";

  // ヘッダー読み込み
  fetch(basePath + "header.html")
    .then(response => response.text())
    .then(data => {
      document.getElementById("header").innerHTML = data;
    });

  // フッター読み込み
  fetch(basePath + "footer.html")
    .then(response => response.text())
    .then(data => {
      document.getElementById("footer").innerHTML = data;
    });
});
```

```
/* css/style.css */
/* 全体リセット */
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

/* ベーススタイル */
body {
  font-family: "Helvetica Neue", Arial, sans-serif;
  line-height: 1.6;
  background-color: #f9f9f9;
  color: #333;
}

/* ヘッダー */
site-header {
    background-color: #fff;
    padding: 20px;
    position: fixed;
    top: 0;
    left: 0; /* Added for full width */
    right: 0; /* Added for full width */
    z-index: 1000;
    box-shadow: 0 2px 4px rgba(0,0,0,0.05);
}
.header-container {
    width: 100%;
    max-width: 1200px;
    margin: 0 auto;
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 0 20px; /* Added for padding on smaller screens */
    box-sizing: border-box; /* Ensures padding is included in width */
}
a.logo:link,
a.logo:visited,
a.logo:hover,
a.logo:active {
  color: inherit;   /* spanの色をそのまま使う */
  text-decoration: none;
}
.logo {
    font-size: 28px;
    font-weight: bold;
    letter-spacing: 1.5px;
    transition: color 0.3s;
}
.logo:hover {
    color: #00aaff;
}

.logo-tool {
    color: #008080;
}
.logo-box {
    color: #666666;
}

/* メイン部分 */
main {
  padding: 2rem;
}

.intro {
  background: #fff;
  padding: 1.5rem;
  border-radius: 8px;
  box-shadow: 0 2px 5px rgba(0,0,0,0.1);
}

/* フッター */
footer {
  text-align: center;
  padding: 1rem;
  margin-top: 2rem;
  background-color: #eee;
}

```


```
/* tools.css - 各ツール専用スタイル
   前提: css/style.css を読み込んだ後に読み込む
*/

/* 変数（カスタマイズしやすく） */
:root{
  --tool-bg: #ffffff;
  --tool-foreground: #1f2937;
  --muted: #6b7280;
  --accent: #2563eb;
  --accent-2: #2c7a7b;
  --radius: 8px;
  --shadow: 0 6px 18px rgba(15,23,42,0.06);
  --control-padding: 0.55rem;
  --max-width: 780px;
}

/* 共通コンテナ */
.tool-wrapper {
  max-width: var(--max-width);
  margin: 1.25rem auto;
  background: var(--tool-bg);
  color: var(--tool-foreground);
  padding: 1rem;
  border-radius: var(--radius);
  box-shadow: var(--shadow);
}

/* 見出し */
.tool-wrapper h1,
.tool-wrapper h2 {
  margin-bottom: 0.6rem;
  color: var(--tool-foreground);
  line-height: 1.2;
}

/* ラベルとフォーム列 */
.form-row {
  display: flex;
  gap: 0.6rem;
  align-items: center;
  margin-bottom: 0.75rem;
}
.form-row label {
  min-width: 96px;
  font-weight: 600;
  color: var(--muted);
  font-size: 0.95rem;
}

/* 汎用入力スタイル */
input[type="text"],
input[type="number"],
select,
textarea {
  width: 100%;
  padding: var(--control-padding);
  border: 1px solid #e6e9ee;
  border-radius: 8px;
  font-size: 1rem;
  color: var(--tool-foreground);
  background: #fff;
  outline: none;
}
textarea { min-height: 120px; resize: vertical; }

/* フォーカス時の視認性向上 */
input:focus, textarea:focus, select:focus {
  box-shadow: 0 0 0 4px rgba(37,99,235,0.08);
  border-color: var(--accent);
}

/* ボタン共通 */
.btn {
  display: inline-flex;
  align-items: center;
  gap: .45rem;
  padding: 0.55rem 0.9rem;
  border-radius: 8px;
  border: none;
  background: var(--accent);
  color: #fff;
  font-weight: 600;
  cursor: pointer;
  transition: transform .06s ease, box-shadow .06s ease, opacity .08s;
}
.btn.secondary {
  background: #475569;
}
.btn:active { transform: translateY(1px); }
.btn[disabled] { opacity: .6; cursor: not-allowed; }

/* 小さな補助ボタン群 */
.btn-ghost {
  background: transparent;
  color: var(--accent);
  border: 1px dashed #dbeafe;
}

/* 結果表示ボックス */
.output-box,
.result-box {
  background: #f8fafc;
  border: 1px solid #eef2ff;
  padding: 0.9rem;
  border-radius: 8px;
  margin-top: 0.6rem;
  color: var(--tool-foreground);
}

/* 電卓固有 */
.calculator {
  display: grid;
  gap: 0.9rem;
}
.calculator #display {
  width: 100%;
  padding: 0.6rem;
  font-size: 1.4rem;
  text-align: right;
  border-radius: 8px;
  border: 1px solid #e6e9ee;
  background: #ffffff;
}
.buttons {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 0.5rem;
}
.buttons .btn {
  padding: 0.9rem 0.6rem;
  font-size: 1.05rem;
  border-radius: 6px;
  background: #f3f4f6;
  color: var(--tool-foreground);
  box-shadow: none;
}
.buttons .btn.operator { background: #e2e8f0; font-weight:700; }
.buttons .btn.equal {
  grid-column: span 4;
  background: var(--accent);
  color: #fff;
  font-size: 1.05rem;
}

/* 翻訳（translator）固有 */
.translator .options {
  display: flex;
  gap: 0.6rem;
  align-items: center;
  margin-top: 0.6rem;
  flex-wrap: wrap;
}
.translator .output-box {
  white-space: pre-wrap;
  min-height: 3.2rem;
}

/* 文字数カウンタ・トークンカウンタ */
.counter .result-box,
.token-counter .result-box {
  display: flex;
  gap: 1.2rem;
  align-items: center;
  justify-content: flex-start;
  flex-wrap: wrap;
}
.counter .result-box p,
.token-counter .result-box p { margin: 0; font-weight:600; color:var(--muted); }

/* QRジェネレータ固有 */
.qr-container {
  display: grid;
  gap: 0.8rem;
}
.qr-container .controls {
  display:flex;
  gap:0.6rem;
  flex-wrap:wrap;
}
.qr-container canvas {
  max-width: 100%;
  height: auto;
  border-radius: 6px;
  background: #fff;
  margin-top: 0.4rem;
  border: 1px solid #eceff6;
}

/* 小さい補助テキスト */
.note {
  color: var(--muted);
  font-size: 0.9rem;
  margin-top: 0.4rem;
}

/* レスポンシブ（スマホ最適化） */
@media (max-width: 560px) {
  .form-row { flex-direction: column; align-items: stretch; }
  .form-row label { min-width: auto; margin-bottom: 0.25rem; }
  .buttons { grid-template-columns: repeat(4, 1fr); }
  .btn { width: 100%; justify-content: center; }
}

/* アクセシビリティ向け：フォーカスリングの高コントラスト版 */
:focus {
  outline: 3px solid rgba(37,99,235,0.18);
  outline-offset: 2px;
}

/* ヘルパークラス */
.text-muted { color: var(--muted); font-size:0.95rem; }
.kv { display:flex; gap:0.4rem; align-items:center; }

/* 画面幅が広いときは中央寄せの余白を増やす */
@media (min-width: 1200px) {
  .tool-wrapper { padding: 1.5rem 2rem; }
}
```
