[[P05 Webページ制作はどこから始めるべきか？おすすめの手順ガイド]]

ジャンル・カテゴリ：ハウツー

## 目次
[[#はじめに]]  
[[#indexhtml]]  
[[#cssstylecss]]  
[[#jsmainjs]]  
[[#まとめ]]  

## はじめに
ここでは、Webツール集の最初の土台となる **index.html**、**css/style.css**、**js/main.js** の基本的なコード例を紹介します。後から個別ツール（電卓・翻訳・文字数カウンタなど）を追加していく前提で、シンプルかつ拡張しやすい構成にしています。

## index.html
```html
<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Toolbox - Webツール集</title>
  <link rel="stylesheet" href="css/style.css">
</head>
<body>
  <header>
    <h1>Toolbox</h1>
    <nav>
      <ul>
        <li><a href="tools/calculator.html">電卓</a></li>
        <li><a href="tools/translator.html">翻訳</a></li>
        <li><a href="tools/char-counter.html">文字数カウント</a></li>
        <li><a href="tools/token-counter.html">トークン数カウント</a></li>
        <li><a href="tools/qr-generator.html">QRコード生成</a></li>
      </ul>
    </nav>
  </header>
  <main>
    <section class="intro">
      <h2>ようこそ Toolbox へ</h2>
      <p>日常作業や開発に役立つ小さなツールをまとめたWebページです。</p>
      <p>上のナビゲーションから利用したいツールを選んでください。</p>
    </section>
  </main>
  <footer>
    <p>&copy; 2025 Toolbox</p>
  </footer>
  <script src="js/main.js"></script>
</body>
</html>
```

```css
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
header {
  background-color: #2c3e50;
  color: #fff;
  padding: 1rem;
}

header h1 {
  margin-bottom: 0.5rem;
}

nav ul {
  list-style: none;
  display: flex;
  gap: 1rem;
}

nav a {
  color: #fff;
  text-decoration: none;
}

nav a:hover {
  text-decoration: underline;
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

```js
// main.js - 共通スクリプト

document.addEventListener("DOMContentLoaded", () => {
  console.log("Toolboxへようこそ！");

  // ナビゲーションリンクをクリックしたときの簡易エフェクト
  const navLinks = document.querySelectorAll("nav a");
  navLinks.forEach(link => {
    link.addEventListener("click", () => {
      alert(`「${link.textContent}」ページへ移動します。`);
    });
  });
});
```

まとめ
- index.html でトップページとツールへのリンクを用意
- css/style.css で基本デザインを整備
- js/main.js で共通の動作や確認用スクリプトを追加

この3つを整えることで、各ツールページを追加する準備が整います。

---

※この記事はChatGPTの回答を基に作成しています。

[[P07 電卓ページ（calculator.html & calculator.js）のコード例]]