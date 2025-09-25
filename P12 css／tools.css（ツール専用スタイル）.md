[[P11 QRコード生成ページ（tools／qr-generator.html）]]

ジャンル・カテゴリ：ハウツー

## 目次
[[#はじめに]]
[[#目的と方針]]
[[#コード（css/tools.css）]]
[[#補足（追加で用意すると良いもの）]]
[[#使い方のポイント]]

## はじめに
tools フォルダ内の各ツールページ（電卓、翻訳、文字数カウント、トークンカウンタ、QRジェネレータ）専用のスタイルです。`css/style.css` の上に読み込む想定で、「各ツール固有のレイアウト／コンポーネント」を整理しています。軽量で読みやすく、レスポンシブに対応するように設計しています。

## 目的と方針
- 各ツールのUI を統一しつつ、ツール固有の見やすさを確保する  
- 小さな画面（スマホ）でも操作しやすいレイアウトにする  
- アクセシビリティ（フォーカス表示、コントラスト）を最低限担保する  
- 将来のカスタマイズ（色・サイズ・トークン表現など）をしやすく変数を用意する

## コード（css/tools.css）
```css
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

## 補足（追加で用意すると良いもの）
• カラー変数を :root で用意しているため、ダークモード対応やブランドカラー反映が簡単にできます。
• アイコン用に [Feather Icons] や [Heroicons] のSVGを設置しておくと、ボタン表示が分かりやすくなります。
• 電卓のキーサイズやフォントは可視性に影響するため、実機（スマホ・タブレット）での確認を推奨します。
• トークンカウント（token-counter）は表示上の改善（例：トークン内訳やサンプル置換）用に追加スタイルがあると便利です。
• tools.css は軽量に保ち、共通のユーティリティは style.css に置くとメンテナンスしやすいです。

## 使い方のポイント
• index.html や各ツールページ側で、<link rel="stylesheet" href="../css/tools.css"> を確実に読み込んでください（パスは配置に合わせて調整）。
• 追加のJSで新しいUI要素（モーダル、トースト等）を導入する場合は、.tool-wrapper の中に挿入して既存のスタイルと干渉しないようにしてください。
• アクセシビリティ確認（キーボード操作、スクリーンリーダでの読み上げ）も併せて行ってください。

---


※この記事はChatGPTの回答を基に作成しています。

[[P13 index.html（便利ツールポータルページ 改良版）]]