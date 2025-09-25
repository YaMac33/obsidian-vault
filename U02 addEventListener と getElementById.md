[[U01 コーディングにおける「バリデーション」とは何か]]

JavaScriptでよく使われる **`addEventListener`** と **`getElementById`** について、基礎から分かりやすく説明しますね。

---

# `getElementById` とは？

- **役割**: HTMLの要素を **ID属性** で取得するメソッドです。
    
- **書き方**:
    
    ```javascript
    document.getElementById("id名");
    ```
    
- **戻り値**: そのIDを持つ **最初の要素** を返します。
    
    - 存在しなければ `null` を返します。
        

### 例

```html
<p id="message">こんにちは！</p>
<script>
  const element = document.getElementById("message");
  console.log(element.textContent); // → "こんにちは！"
</script>
```

👉 ポイント:

- IDはHTML内で **一意（ユニーク）** であることが前提です。
    
- クラスやタグ名ではなく、IDだけでピンポイントに要素を取れるのが特徴。
    

---

# `addEventListener` とは？

- **役割**: 特定の要素に対して **イベント（クリック・入力・キー操作など）** を登録するメソッドです。
    
- **書き方**:
    
    ```javascript
    element.addEventListener("イベント名", 実行する関数);
    ```
    

### 例

```html
<button id="myBtn">押してね</button>
<script>
  const btn = document.getElementById("myBtn");

  // ボタンがクリックされたときに実行
  btn.addEventListener("click", function() {
    alert("ボタンが押されました！");
  });
</script>
```

👉 ポイント:

- `"click"`, `"input"`, `"mouseover"` など、様々なイベントを指定可能。
    
- 複数回使って、1つの要素に複数のイベント処理を登録できる。
    
- HTMLに直接 `onclick="..."` と書くより、**コードの管理がしやすい**。
    

---

# よくある組み合わせ

`getElementById` で要素を取得して、`addEventListener` で動きをつけるのが基本パターンです。

```html
<input type="text" id="nameInput" placeholder="名前を入力">
<button id="greetBtn">挨拶する</button>
<p id="output"></p>

<script>
  const input = document.getElementById("nameInput");
  const button = document.getElementById("greetBtn");
  const output = document.getElementById("output");

  button.addEventListener("click", function() {
    output.textContent = "こんにちは、" + input.value + "さん！";
  });
</script>
```

👉 これで「名前を入力してボタンを押すと挨拶が表示される」シンプルなアプリが作れます。

---

# まとめ

- `getElementById`: **IDで要素を取得**
    
- `addEventListener`: **イベントを登録**して動きをつける
    
- セットで使うと、ボタンや入力欄を動的に操作できる
    

---

[[U03 const とは？]]