[[P10トークン数カウントページ（token-counter.html & token-counter.js）のコード例]]

ジャンル・カテゴリ：ハウツー

## 目次
[[#はじめに]]  
[[#機能要件]]  
[[#qr-generatorhtml（コード）]]  
[[#補足（動作確認・拡張案）]]  

## はじめに
このページは、テキストやURLからQRコードを生成し、プレビュー表示・ダウンロードできるシンプルな単一ファイル（HTML）実装例です。外部ライブラリ **QRious**（軽量で canvas ベース）をCDNから読み込み、即座に動作するようにしています。別ファイルの `js/qr-generator.js` を作る必要はありませんが、分割したい場合は脚注を参照してください。

## 機能要件
- テキスト／URLを入力してQRコードを生成する  
- サイズ指定（px）で出力サイズを変更できる  
- エラーレベル（低〜高）を選択可能  
- 生成したQRコードを画像（PNG）としてダウンロード可能  
- モバイルでも使えるレスポンシブなレイアウト

## qr-generator.html（コード）
```html
<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>QRコード生成 - Toolbox</title>
  <link rel="stylesheet" href="../css/style.css" />
  <link rel="stylesheet" href="../css/tools.css" />
  <style>
    /* ページ固有の簡易スタイル（必要に応じて外部CSSへ分離） */
    .qr-container { max-width:720px; margin:1.5rem auto; padding:1rem; background:#fff; border-radius:8px; box-shadow:0 2px 6px rgba(0,0,0,0.06); }
    .form-row { display:flex; gap:0.5rem; margin-bottom:0.75rem; align-items:center; }
    .form-row label { min-width:84px; font-weight:600; }
    .form-row input[type="text"], .form-row select, .form-row input[type="number"] { flex:1; padding:0.5rem; border:1px solid #ddd; border-radius:6px; }
    textarea { width:100%; min-height:100px; padding:0.6rem; border:1px solid #ddd; border-radius:6px; resize:vertical; }
    .controls { display:flex; gap:0.5rem; margin-top:0.75rem; }
    button { padding:0.6rem 0.9rem; border-radius:6px; border:none; cursor:pointer; background:#2c3e50; color:#fff; }
    button.secondary { background:#4a5568; }
    .preview { margin-top:1rem; text-align:center; }
    canvas { background:#fff; border:1px solid #eee; border-radius:6px; }
    .note { margin-top:0.5rem; color:#666; font-size:0.9rem; }
    @media (max-width:480px) { .form-row { flex-direction:column; align-items:stretch; } .form-row label { min-width:auto; } }
  </style>
</head>
<body>
  <header>
    <h1>QRコード生成</h1>
    <nav><a href="../index.html">← トップへ戻る</a></nav>
  </header>

  <main>
    <section class="qr-container">
      <div>
        <label for="inputText">テキスト / URL</label>
        <textarea id="inputText" placeholder="例: https://example.com または 任意のテキスト"></textarea>
      </div>

      <div class="form-row">
        <label for="size">サイズ (px)</label>
        <input id="size" type="number" value="256" min="64" max="2048" />
      </div>

      <div class="form-row">
        <label for="level">エラーレベル</label>
        <select id="level">
          <option value="L">L — Low（7%）</option>
          <option value="M" selected>M — Medium（15%）</option>
          <option value="Q">Q — Quartile（25%）</option>
          <option value="H">H — High（30%）</option>
        </select>
      </div>

      <div class="controls">
        <button id="generateBtn">生成</button>
        <button id="downloadBtn" class="secondary">ダウンロード (PNG)</button>
        <button id="clearBtn" class="secondary">クリア</button>
      </div>

      <div class="preview" aria-live="polite">
        <h2>プレビュー</h2>
        <!-- QRious は canvas を使うのでキャンバス要素を用意 -->
        <canvas id="qrCanvas" width="256" height="256" role="img" aria-label="QRコードプレビュー"></canvas>
        <p class="note">※QRコードは読み取り精度に影響するため、余白や縮小に注意してください。</p>
      </div>
    </section>
  </main>

  <footer>
    <p>&copy; 2025 Toolbox</p>
  </footer>

  <!-- QRious（軽量なQR生成ライブラリ）をCDNから読み込む -->
  <script src="https://cdnjs.cloudflare.com/ajax/libs/qrious/4.0.2/qrious.min.js" integrity="sha512-+nKf1tG6NJ7f4+7A3I8Y6o1i4E7o1oJk7s3r4bqk2P0Z0NnC4M1lVgMZ5v3u6m1jJ2Yf6cN9d2sLh2e3f7w9A==" crossorigin="anonymous" referrerpolicy="no-referrer"></script>

  <script>
    // これだけで動くシンプルなローカル実装
    document.addEventListener('DOMContentLoaded', () => {
      const inputText = document.getElementById('inputText');
      const sizeInput = document.getElementById('size');
      const levelSelect = document.getElementById('level');
      const generateBtn = document.getElementById('generateBtn');
      const downloadBtn = document.getElementById('downloadBtn');
      const clearBtn = document.getElementById('clearBtn');
      const canvas = document.getElementById('qrCanvas');

      // QRious インスタンス（canvas 要素に紐付け）
      const qr = new QRious({ element: canvas, value: '', size: Number(sizeInput.value), level: levelSelect.value });

      const generate = () => {
        const text = inputText.value.trim();
        if (!text) {
          alert('テキストを入力してください。');
          return;
        }
        const size = Math.max(64, Math.min(2048, Number(sizeInput.value) || 256));
        qr.size = size;
        qr.level = levelSelect.value;
        qr.value = text;
      };

      const downloadPNG = () => {
        // canvas を PNG 画像としてダウンロード
        if (!qr.value) {
          alert('まずQRコードを生成してください。');
          return;
        }
        const dataURL = canvas.toDataURL('image/png');
        const a = document.createElement('a');
        a.href = dataURL;
        // ファイル名に簡易的な日付と短縮テキストを付与
        const safeName = qr.value.slice(0,20).replace(/[^a-z0-9_\-]/ig, '_') || 'qr';
        a.download = `qr_${safeName}_${new Date().toISOString().slice(0,19).replace(/[:T]/g,'-')}.png`;
        document.body.appendChild(a);
        a.click();
        a.remove();
      };

      generateBtn.addEventListener('click', generate);
      downloadBtn.addEventListener('click', downloadPNG);
      clearBtn.addEventListener('click', () => {
        inputText.value = '';
        qr.value = '';
        // canvas をクリア（白で塗りつぶす）
        const ctx = canvas.getContext('2d');
        ctx.clearRect(0, 0, canvas.width, canvas.height);
      });

      // Enterで生成（textareaではShift+Enterで改行）
      inputText.addEventListener('keydown', (e) => {
        if (e.key === 'Enter' && !e.shiftKey) {
          e.preventDefault();
          generate();
        }
      });
    });
  </script>
</body>
</html>
```

## 補足（動作確認・拡張案）
- このHTMLは単体で動作します（qrious を CDN から読み込んでいるため）。オフラインで使う場合はライブラリをローカルに置いて参照してください。
- 生成ロジックを外部ファイルに分けるなら js/qr-generator.js を作成し、<script src="../js/qr-generator.js"></script> に置き換えてください。
- 実運用／大量生成向けにはサーバー側で生成（画像キャッシュ）する方法や、ロゴ埋め込み・色指定機能（QRiousでも可能）を追加すると便利です。
- 商用利用や厳密なトラッキングを行う場合は、生成画像の短辺に十分な余白（quiet zone）を確保するテストを行ってください。

---

※この記事はChatGPTの回答を基に作成しています。

[[P12 css／tools.css（ツール専用スタイル）]]