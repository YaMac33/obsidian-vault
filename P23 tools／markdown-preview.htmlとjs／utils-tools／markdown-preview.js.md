[[P22 tools／color-name.html（UI部分）と js／dev-design／color-name.js（ロジック部分）のサンプルコード]]

Markdownの入力をプレビューできるシンプルなツールを作ります。

- `tools/markdown-preview.html` → 入力欄とプレビューエリアを用意。
    
- `js/utils-tools/markdown-preview.js` → 入力をリアルタイムにMarkdownに変換して表示。
    

ライブラリには軽量で有名な [marked.js](https://github.com/markedjs/marked) を使う想定にします（CDNで読み込み）。

---

### `tools/markdown-preview.html`

```html
<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8">
  <title>Markdownプレビュー</title>
  <link rel="stylesheet" href="../css/style.css">
  <style>
    .markdown-container {
      display: flex;
      gap: 20px;
    }
    textarea {
      width: 45%;
      height: 400px;
      padding: 10px;
      font-size: 14px;
      font-family: monospace;
    }
    #preview {
      width: 45%;
      height: 400px;
      overflow-y: auto;
      padding: 10px;
      border: 1px solid #ccc;
      background: #fafafa;
    }
  </style>
</head>
<body>
  <main>
    <section class="tool-container">
      <h1>Markdownプレビュー</h1>
      <div class="markdown-container">
        <textarea id="markdownInput" placeholder="ここにMarkdownを書いてください..."></textarea>
        <div id="preview"></div>
      </div>
    </section>
  </main>

  <!-- marked.js CDN -->
  <script src="https://cdn.jsdelivr.net/npm/marked/marked.min.js"></script>
  <script type="module" src="../js/utils-tools/markdown-preview.js"></script>
</body>
</html>
```

---

### `js/utils-tools/markdown-preview.js`

```js
// js/utils-tools/markdown-preview.js

const markdownInput = document.getElementById("markdownInput");
const preview = document.getElementById("preview");

// 初期サンプルテキスト
const sampleText = `# Markdown プレビュー

これは **リアルタイム変換** のサンプルです。

- 箇条書き
- *イタリック*
- [リンク](https://example.com)

> 引用ブロック

\`\`\`js
console.log("コードブロックもOK！");
\`\`\`
`;

markdownInput.value = sampleText;
preview.innerHTML = marked.parse(sampleText);

// 入力が変わるたびに変換
markdownInput.addEventListener("input", () => {
  const text = markdownInput.value;
  preview.innerHTML = marked.parse(text);
});
```

---

これで、

- 左の `textarea` にMarkdownを入力
    
- 右側に即時プレビュー
    

ができます。

---

質問です！  
👉 このMarkdownプレビュー、**「リアルタイム表示」だけでOK**ですか？  
それとも **「HTML出力をコピー」できるボタン**も欲しいですか？

[[P24 tools／timer-and-stopwatch.htmlとjs／utils-tools／timer-and-stopwatch.js]]
