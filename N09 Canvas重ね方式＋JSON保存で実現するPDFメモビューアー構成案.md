[[N08 PDFにメモ書きを重ねるWebビューアーは実現できる？]]

## はじめに
ここでは「PDFを表示し、その上にCanvasを重ねてメモ書きを可能にし、JSON形式で保存する」構成案を紹介します。本記事は**ハウツー系**のカテゴリに該当します。

## 全体構成
### 使用技術
- **GitHub Pages**：ホスティング（静的ファイル）
- **PDF.js**：PDFをCanvasに描画
- **HTML Canvas（annotation-layer）**：メモ書き用レイヤー
- **JavaScript**：描画制御、JSON保存／読み込み
- **LocalStorage or Google Drive API**：メモデータ保存先

### アーキテクチャ概要
```
[GitHub Pages] –(HTML/JS/CSS)–> [ブラウザ]
├── PDF.js (PDF描画)
├── Canvas (メモレイヤー)
└── JSON保存 (LocalStorage or Drive API)
```

## 実装ステップ
### 1. PDF表示部分
- PDF.jsでPDFを読み込み
- ページごとにCanvasへ描画
- `#pdf-canvas`としてDOMに配置

```
<canvas id="pdf-canvas"></canvas>
<canvas id="annotation-layer"></canvas>
```

## 2. メモ書きレイヤー
- `annotation-layer`は透明背景のCanvas  
- マウス/タッチイベントをキャプチャし線を描画  
- 線のデータ（x座標、y座標、色、太さなど）を配列に格納  

---

## 3. JSON保存方式
- メモデータはオブジェクト形式で管理  
```
{
  "page1": [
    {"x": 100, "y": 200, "color": "red", "size": 2},
    {"x": 110, "y": 210, "color": "red", "size": 2}
  ],
  "page2": []
}
```

### 保存先の選択肢
- LocalStorage（シンプル、端末ごとに保存）  
- IndexedDB（大量データ対応）  
- Google Drive API（複数端末から利用可能）  

---

## 4. 再描画処理
- PDF.jsでページを表示後、対応するメモデータをJSONから読み込み  
- Canvasに再描画して前回のメモを復元  

---

## 5. ユーザー操作
- 「書く」「消す」「保存」「読み込み」などのUIボタンを配置  

### 例：
- 📝 書くモード（ペンツール）  
- ❌ 消すモード（消しゴム）  
- 💾 保存（JSONエクスポート／LocalStorage保存）  
- 📂 読み込み（JSONインポート／LocalStorageから復元）  

---

## メリットとデメリット

### メリット
- 実装がシンプルで早い  
- PDF自体を変更しないため安全  
- JSON保存なので管理が容易  

### デメリット
- PDF単体ではメモが見えない（再現にはWebアプリ必須）  
- JSONとPDFのファイルが分離するため管理が煩雑になる可能性  

---

## まとめ
- Canvas重ね方式＋JSON保存は最もシンプルにメモ付きPDFビューアーを作る方法  
- スタンドアロン利用ならLocalStorage、マルチデバイス対応ならGoogle Drive APIが有効  
- UIを工夫すればユーザーが直感的に操作可能  

---

※この記事はChatGPTの回答を基に作成しています。

[[N10 iPad向け：Google Drive API（＋ブラウザローカル）の無料枠と代替サービスまとめ — PDF注釈（JSON保存）運用向けガイド]]