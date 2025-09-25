[[U04 HTML と JavaScript を連携させる代表的なメソッド・API]]

---

## ① HTML内の

```html
<script src="https://cdn.jsdelivr.net/npm/marked/marked.min.js"></script>
```

### これは何をしているか？

- **外部ライブラリ（marked.js）を読み込む**ための記述です。
    
- `marked.js` は、Markdown形式のテキストをHTMLに変換してくれるライブラリ。
    
- `cdn.jsdelivr.net` は CDN（Content Delivery Network）で、世界中のサーバーからライブラリを高速に配信してくれる仕組み。
    

### ポイント

- これを読み込むことで、ブラウザ上で `marked` というグローバルオブジェクトが使えるようになります。
    
- つまり、JavaScriptの中で
    
    ```js
    marked.parse("**太字**")
    ```
    
    と書けば、Markdownの `**太字**` が `<strong>太字</strong>` に変換されます。
    

👉 **もしこれを読み込まなければ、`marked` が未定義エラーになり、変換機能は動きません。**

---

## ② JS内のサンプルコード部分

```javascript
markdownInput.value = sampleText;
preview.innerHTML = marked.parse(sampleText);

// 入力が変わるたびに変換
markdownInput.addEventListener("input", () => {
  const text = markdownInput.value;
  preview.innerHTML = marked.parse(text);
});
```

ここは3つの処理に分けて解説できます。

---

### (1) `markdownInput.value = sampleText;`

- `markdownInput` は `<textarea>` を指している変数です。
    
- `.value` は入力欄の中身を意味します。
    
- `sampleText` というサンプル文字列を代入することで、ページを開いたときにすでにMarkdownの例文が入力された状態になります。
    

👉 「初期表示テキストを入れる」処理です。

---

### (2) `preview.innerHTML = marked.parse(sampleText);`

- `preview` は右側のプレビュー領域 `<div id="preview">` を指しています。
    
- `.innerHTML` は、その要素の中に **HTMLコード** を挿入するためのプロパティ。
    
- `marked.parse(sampleText)` で Markdown を HTML に変換しています。  
    例：
    
    ```markdown
    # タイトル
    ```
    
    が
    
    ```html
    <h1>タイトル</h1>
    ```
    
    に変換されて `preview` 内に表示されます。
    

👉 「サンプルテキストを最初にプレビューに表示する」処理です。

---

### (3) 入力イベントの監視

```javascript
markdownInput.addEventListener("input", () => {
  const text = markdownInput.value;
  preview.innerHTML = marked.parse(text);
});
```

#### 仕組み

1. `addEventListener("input", …)`
    
    - ユーザーが `<textarea>` に文字を入力するたびにイベントが発生します。
        
    - 「キーを押した」「貼り付けた」など、入力が変わるたびに反応。
        
2. `const text = markdownInput.value;`
    
    - 入力欄の最新の内容を取得します。
        
3. `preview.innerHTML = marked.parse(text);`
    
    - その内容を `marked.parse()` でHTMLに変換し、プレビュー領域に反映します。
        

👉 「入力した瞬間に右側のプレビューがリアルタイム更新される」処理です。

---

## まとめると

- **HTMLの `<script src="...">`**  
    → `marked.js` という「Markdown → HTML変換エンジン」をブラウザに読み込む。
    
- **JSのサンプルコード部分**
    
    - `value = sampleText;` : 初期テキストを入力欄にセット。
        
    - `innerHTML = marked.parse(...)` : MarkdownをHTMLに変換してプレビュー表示。
        
    - `addEventListener("input", …)` : 入力のたびに自動で変換して即時プレビュー。
        

---

質問です！  
👉 この解説をさらに「図解（フロー図や処理の流れ）」で視覚的にまとめると分かりやすいと思いますが、イラスト風の図解も作りましょうか？

[[U06 GitHub のソースコードの探し方]]
