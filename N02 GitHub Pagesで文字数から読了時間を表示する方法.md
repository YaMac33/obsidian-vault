[[N01 GitHub Pagesで翻訳Webアプリを開発するためのファイル構成案]]

## はじめに
この記事では、**GitHub Pagesで公開しているWebページに「文字数から読了時間を表示する機能」を追加する方法**を解説します。ジャンルは「ハウツー」にあたり、Web制作初心者でも取り入れやすい構成を意識しています。

## 実装の流れ
1. ページの本文テキストを取得する  
2. 文字数をカウントする  
3. 標準的な読書速度を基に読了時間を計算する  
4. ページ内に表示する  

## 標準的な読書速度の目安
- 日本語の場合: 約400〜600文字/分  
- 英語の場合: 約200〜250語/分  
本記事では「400文字/分」を基準にします。

## 実装例（JavaScript）
以下のスクリプトをHTMLの`<body>`閉じタグ直前に追加します。

```html
<div id="read-time"></div>
<script>
  // 記事本文を取得（例: mainタグ内のテキスト）
  const articleText = document.querySelector("main")?.innerText || "";
  
  // 文字数をカウント
  const charCount = articleText.length;
  
  // 読了時間（分）を計算
  const readingSpeed = 400; // 1分あたりの文字数
  const minutes = Math.ceil(charCount / readingSpeed);
  
  // 表示
  document.getElementById("read-time").innerText = 
    `この記事は約${charCount}文字、読了目安は約${minutes}分です。`;
</script>
```

## ポイント
## テキスト取得範囲を調整
- mainタグやarticleタグを指定すると記事本文のみを対象にできる
- サイドバーやフッターの文字数を含めない工夫が必要

### デザインの工夫
- 表示位置は記事冒頭がベスト
- CSSでアイコンやスタイルを付けて視認性を高める

### GitHub Pagesでの利用
- HTML直書き、もしくはJekyllテーマのレイアウトファイルに追加可能
- 外部ライブラリは不要で、純粋なJavaScriptのみで動作

## まとめ
GitHub Pagesに文字数カウント×読了時間表示機能を加えることで、読者にとって記事の見通しが良くなり、最後まで読んでもらいやすくなります。カスタマイズはシンプルで、どんなブログやポートフォリオにも応用可能です。

  
※この記事はChatGPTの回答を基に作成しています。

[[N03 GitHub Pagesで(root)やdocs以外のディレクトリを公開する方法]]


