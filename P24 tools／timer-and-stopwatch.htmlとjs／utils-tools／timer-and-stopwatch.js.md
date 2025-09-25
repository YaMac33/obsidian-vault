 [[P23 tools／markdown-preview.htmlとjs／utils-tools／markdown-preview.js]]

---
## 機能想定

- **ストップウォッチ**
    
    - 開始 / 一時停止 / リセット
        
    - 経過時間を「分:秒:ミリ秒」で表示
        
- **タイマー**
    
    - 分と秒を入力して開始
        
    - カウントダウン表示
        
    - 停止 / リセット可能
        

---

## `tools/timer-and-stopwatch.html`

```html
<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8">
  <title>タイマー & ストップウォッチ</title>
  <link rel="stylesheet" href="../css/style.css">
  <style>
    .tool-container {
      max-width: 500px;
      margin: auto;
      padding: 20px;
    }
    .section {
      margin-bottom: 40px;
    }
    .display {
      font-size: 2em;
      margin: 10px 0;
    }
    button {
      margin: 5px;
    }
  </style>
</head>
<body>
  <main>
    <section class="tool-container">
      <h1>タイマー & ストップウォッチ</h1>

      <!-- ストップウォッチ -->
      <div class="section">
        <h2>ストップウォッチ</h2>
        <div id="stopwatchDisplay" class="display">00:00.000</div>
        <button id="startStopwatch">開始</button>
        <button id="pauseStopwatch">一時停止</button>
        <button id="resetStopwatch">リセット</button>
      </div>

      <!-- タイマー -->
      <div class="section">
        <h2>タイマー</h2>
        <label>分: <input type="number" id="timerMinutes" min="0" value="0"></label>
        <label>秒: <input type="number" id="timerSeconds" min="0" value="30"></label>
        <div id="timerDisplay" class="display">00:30</div>
        <button id="startTimer">開始</button>
        <button id="pauseTimer">一時停止</button>
        <button id="resetTimer">リセット</button>
      </div>
    </section>
  </main>

  <script type="module" src="../js/utils-tools/timer-and-stopwatch.js"></script>
</body>
</html>
```

---

## `js/utils-tools/timer-and-stopwatch.js`

```js
// ==============================
// ストップウォッチ
// ==============================
const stopwatchDisplay = document.getElementById("stopwatchDisplay");
const startStopwatchBtn = document.getElementById("startStopwatch");
const pauseStopwatchBtn = document.getElementById("pauseStopwatch");
const resetStopwatchBtn = document.getElementById("resetStopwatch");

let stopwatchInterval;
let stopwatchStartTime;
let stopwatchElapsed = 0;
let stopwatchRunning = false;

function updateStopwatch() {
  const now = Date.now();
  const diff = now - stopwatchStartTime + stopwatchElapsed;

  const minutes = Math.floor(diff / 60000);
  const seconds = Math.floor((diff % 60000) / 1000);
  const milliseconds = diff % 1000;

  stopwatchDisplay.textContent =
    `${String(minutes).padStart(2, "0")}:` +
    `${String(seconds).padStart(2, "0")}.` +
    `${String(milliseconds).padStart(3, "0")}`;
}

startStopwatchBtn.addEventListener("click", () => {
  if (!stopwatchRunning) {
    stopwatchStartTime = Date.now();
    stopwatchInterval = setInterval(updateStopwatch, 10);
    stopwatchRunning = true;
  }
});

pauseStopwatchBtn.addEventListener("click", () => {
  if (stopwatchRunning) {
    clearInterval(stopwatchInterval);
    stopwatchElapsed += Date.now() - stopwatchStartTime;
    stopwatchRunning = false;
  }
});

resetStopwatchBtn.addEventListener("click", () => {
  clearInterval(stopwatchInterval);
  stopwatchElapsed = 0;
  stopwatchRunning = false;
  stopwatchDisplay.textContent = "00:00.000";
});

// ==============================
// タイマー
// ==============================
const timerMinutesInput = document.getElementById("timerMinutes");
const timerSecondsInput = document.getElementById("timerSeconds");
const timerDisplay = document.getElementById("timerDisplay");
const startTimerBtn = document.getElementById("startTimer");
const pauseTimerBtn = document.getElementById("pauseTimer");
const resetTimerBtn = document.getElementById("resetTimer");

let timerInterval;
let timerRemaining = 0;
let timerRunning = false;

function updateTimerDisplay(ms) {
  const totalSeconds = Math.ceil(ms / 1000);
  const minutes = Math.floor(totalSeconds / 60);
  const seconds = totalSeconds % 60;

  timerDisplay.textContent =
    `${String(minutes).padStart(2, "0")}:` +
    `${String(seconds).padStart(2, "0")}`;
}

startTimerBtn.addEventListener("click", () => {
  if (!timerRunning) {
    if (timerRemaining <= 0) {
      const minutes = parseInt(timerMinutesInput.value, 10) || 0;
      const seconds = parseInt(timerSecondsInput.value, 10) || 0;
      timerRemaining = (minutes * 60 + seconds) * 1000;
    }

    timerInterval = setInterval(() => {
      timerRemaining -= 1000;
      updateTimerDisplay(timerRemaining);

      if (timerRemaining <= 0) {
        clearInterval(timerInterval);
        timerRunning = false;
        alert("タイマー終了！");
      }
    }, 1000);

    timerRunning = true;
  }
});

pauseTimerBtn.addEventListener("click", () => {
  if (timerRunning) {
    clearInterval(timerInterval);
    timerRunning = false;
  }
});

resetTimerBtn.addEventListener("click", () => {
  clearInterval(timerInterval);
  timerRunning = false;
  timerRemaining = 0;
  updateTimerDisplay(0);
});
```

---

✅ これで

- **ストップウォッチ** → 「開始 → 一時停止 → リセット」
    
- **タイマー** → 分・秒を指定して「開始 → 一時停止 → リセット」
    

が使えるようになります。

---

👉 質問です！  
このツールに **「ラップタイム機能（ストップウォッチで途中経過を記録）」** も追加したいですか？

[[P25 tools／password-generator.htmlとjs／utils-tools／password-generator.js]]
