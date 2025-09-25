[[P12 css／tools.css（ツール専用スタイル）]]

ジャンル・カテゴリ：ハウツー

## 目次
[[#はじめに]]
[[#追加工夫と補足点]]
[[#コード（indexhtml）]]
[[#補足説明と運用のコツ]]

## はじめに
これまで作成してきたWebツールを整理し、**共通ヘッダー・フッター**や**コピー機能**、**軽量化**、**GitHub Pages上でのAPI利用の注意点**を踏まえた改良版のトップページ `index.html` を示します。  
このページをベースに、各ツールページへリンクできるように拡張可能です。

## 追加工夫と補足点
- **共通ヘッダー・フッター**  
  ナビゲーションメニューを全ページ共通化し、利用者が迷わないUIを実現  
- **コピー機能**  
  リストの見出しやリンクURLをワンクリックでコピーできるサンプルボタンを設置  
- **軽量化**  
  外部ライブラリを使わず、バニラJS + CSS のみで構成  
- **レスポンス確認**  
  API連携する予定（天気取得・RSS表示など）の箇所はCORS制約に注意  

## コード（index.html）
```html
<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>便利ツールポータル - Toolbox</title>
  <link rel="stylesheet" href="css/style.css">
  <link rel="stylesheet" href="css/tools.css">
  <script defer src="js/main.js"></script>
</head>
<body>
  <!-- 共通ヘッダー -->
  <header class="site-header">
    <div class="container">
      <h1><a href="index.html">Toolbox</a></h1>
      <nav>
        <ul class="nav-links">
          <li><a href="tools/calculator.html">電卓</a></li>
          <li><a href="tools/translator.html">翻訳</a></li>
          <li><a href="tools/char-counter.html">文字数カウント</a></li>
          <li><a href="tools/token-counter.html">トークン数カウント</a></li>
          <li><a href="tools/qr-generator.html">QR生成</a></li>
        </ul>
      </nav>
    </div>
  </header>

  <!-- メインコンテンツ -->
  <main class="tool-wrapper">
    <h2>便利ツールページのアイデア</h2>

    <section>
      <h3>計算系 <button class="btn-ghost copy-btn" data-copy="計算系ツール">コピー</button></h3>
      <ul>
        <li>電卓（四則演算・応用計算）</li>
        <li>BMI計算機</li>
        <li>割り勘計算</li>
        <li>通貨換算・暗号資産換算</li>
      </ul>
    </section>

    <section>
      <h3>文字・文章系 <button class="btn-ghost copy-btn" data-copy="文字・文章系ツール">コピー</button></h3>
      <ul>
        <li>文字数カウント</li>
        <li>トークン数カウント（AI用プロンプトチェック）</li>
        <li>単語数カウント</li>
        <li>文章読みやすさチェック</li>
        <li>ランダム文章生成（例：例文・テスト用）</li>
      </ul>
    </section>

    <section>
      <h3>翻訳・言語系 <button class="btn-ghost copy-btn" data-copy="翻訳・言語系ツール">コピー</button></h3>
      <ul>
        <li>英和/和英翻訳</li>
        <li>簡易文章要約</li>
        <li>単語帳ジェネレーター</li>
        <li>ローマ字変換</li>
      </ul>
    </section>

    <section>
      <h3>便利ツール系 <button class="btn-ghost copy-btn" data-copy="便利ツール系">コピー</button></h3>
      <ul>
        <li>QRコード生成</li>
        <li>日付計算（○日後、○日前）</li>
        <li>タイマー・ストップウォッチ</li>
        <li>天気取得（API連携）</li>
        <li>RSS簡易表示</li>
        <li>色コード確認・カラーサンプル</li>
        <li>正規表現テストページ</li>
        <li>JSON整形・検証</li>
        <li>Markdownプレビュー</li>
        <li>URLエンコード・デコード</li>
        <li>パスワード生成</li>
        <li>ファイル名一括変換（クライアント側のみ）</li>
        <li>IPアドレス確認</li>
      </ul>
    </section>

    <section>
      <h3>開発・デザイン系 <button class="btn-ghost copy-btn" data-copy="開発・デザイン系">コピー</button></h3>
      <ul>
        <li>HTML/CSS/JavaScriptライブ編集</li>
        <li>アイコン検索・プレビュー</li>
        <li>文字フォント比較プレビュー</li>
        <li>色名からカラーコード変換</li>
      </ul>
    </section>
  </main>

  <!-- 共通フッター -->
  <footer class="site-footer">
    <div class="container">
      <p>&copy; 2025 Toolbox | GitHub Pagesで公開中</p>
    </div>
  </footer>

  <!-- コピー機能のJS -->
  <script>
    document.addEventListener("DOMContentLoaded", () => {
      const buttons = document.querySelectorAll(".copy-btn");
      buttons.forEach(btn => {
        btn.addEventListener("click", () => {
          const text = btn.getAttribute("data-copy");
          navigator.clipboard.writeText(text).then(() => {
            btn.textContent = "コピー済み!";
            setTimeout(() => btn.textContent = "コピー", 1500);
          });
        });
      });
    });
  </script>
</body>
</html>
```

## 補足説明と運用のコツ
- ヘッダー・フッターは include 化（例：JSで読み込み）すると、将来の更新が楽になります
- コピー機能は、各リストのカテゴリ名やリンクをコピーする用途に便利
- **外部API利用（天気・RSS）**は GitHub PagesのHTTPS環境ではCORS制限がかかる場合があるため、事前にAPIの仕様を確認する必要があります
- 軽量化のため、Font AwesomeやjQueryは避け、アイコンはSVG inline埋め込みを推奨

---


※この記事はChatGPTの回答を基に作成しています。

[[P14 サイト構成ツリー（便利ツール群をすべて含む）]]