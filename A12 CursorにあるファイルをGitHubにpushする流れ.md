[[A11 期間計算]]


「CursorにあるファイルをGitHubにpushする」流れを整理すると以下の手順になります。

---

## 1. Gitの初期化（まだしていない場合）

まず、Cursor上でそのプロジェクトフォルダを開いて、ターミナルで以下を実行します。

```bash
git init
```

これでローカルリポジトリが作られます。

---

## 2. GitHubでリポジトリを作成

1. GitHubにログイン
2. 「New repository」からリポジトリを作成

   * 名前を入力
   * Public/Privateを選択
   * **Initialize with READMEはチェックしない方がスムーズ**

作成後、GitHubが表示する「リモート追加」のコマンドをコピーしておきます。

---

## 3. リモートリポジトリを追加

Cursorのターミナルで以下を実行。

```bash
git remote add origin https://github.com/<ユーザー名>/<リポジトリ名>.git
```

（URLはGitHubで作成したリポジトリのものに置き換えてください）

---

## 4. ファイルをステージングしてコミット

```bash
git add .
git commit -m "first commit"
```

---

## 5. GitHubにpush

```bash
git branch -M main
git push -u origin main
```

これでGitHubにファイルが反映されます。

---

## 6. Cursor内での補足

CursorはVSCodeベースなので、左側の **Source Control（ソース管理）** からもGUIでステージングやコミット、pushができます。

* 「＋」でステージング
* メッセージを入力して「✓」でコミット
* 「…」メニューから「Push」

---

ここからGitHubにpushするには、次の流れです。

---

## 1. GitHubにリポジトリを作る

1. GitHubにログイン
2. `New repository` をクリック
3. リポジトリ名を決める（例: `obsidian-vault`）
4. **READMEは作らない**（後で競合しにくくなる）
5. `Create repository` を押す

作成後、GitHubが「…またはコマンドをコピーして実行してください」と出してくれるので、そのURLを使います。

---

## 2. ローカルにリモートを登録

Cursorのターミナル（今 `.git` が作られた場所）で：

```bash
git remote add origin https://github.com/<ユーザー名>/<リポジトリ名>.git
```

例：

```bash
git remote add origin https://github.com/mount123/obsidian-vault.git
```

---

## 3. ファイルをGitに登録してコミット

```bash
git add .
git commit -m "first commit"
```

---

## 4. ブランチ名をmainにしてpush

```bash
git branch -M main
git push -u origin main
```

これでGitHubにアップロードされます 🚀

---

## 5. 注意点（Obsidianならでは）

* `.obsidian` フォルダなど設定ファイルもpushされます
  → 個人設定を除外したいなら `.gitignore` を作成して管理すると便利
* iCloud同期もしているので、 **GitHubとiCloud両方で同期** する形になります

---

「Obsidian Vault全体をGitHubで管理」＋「出先ではiPadからブラウザでGitHub更新」＝ **どの端末でも同じ状態を保てるワークフロー** になります。

ただし実際にやるときのメリットと注意点を整理してみます。

---

## ✅ メリット

1. **三重バックアップ**

   * iCloud
   * GitHub
   * ローカル（Cursor）
     → データ消失リスクをかなり減らせます。

2. **履歴管理が強力**

   * Obsidianはノートアプリとしては履歴弱いですが、GitHubを使えば「いつ・何を・どの端末で変更したか」が追えます。

3. **出先での編集が可能**

   * iPadのブラウザでGitHubのファイル編集 → push
   * 帰宅後にCursorで `git pull` → ローカルとObsidianに反映

---

## ⚠️ 注意点

1. **iCloudとGitの同期が衝突する可能性**

   * iCloudが勝手にファイルを同期中にGitHubからpullすると競合することがあります。
   * 解決策：作業前に必ず `git pull`、作業後に必ず `git push` を徹底。

2. **iPadからの編集はやや不便**

   * GitHubのWebエディタでMarkdownを直接編集する形になるので、Obsidian的な快適さはない。
   * 代替策：iPadにもGitクライアント（例: Working Copy）を入れると、Obsidianと同じVaultを直接操作できる。

3. **機微情報の取り扱い**

   * Vaultに個人的なメモ（パスワード・個人情報など）がある場合は、**Privateリポジトリ** にしておくのが安全。

---

## 🚀 運用フロー例

1. **PC（Cursor＋Obsidian）メイン作業**

   * 作業開始前 → `git pull`
   * 作業後 → `git add . && git commit -m "update" && git push`

2. **iPad（出先）**

   * GitHubのブラウザ or Gitアプリで直接編集・コミット
   * PCに戻ったら → `git pull`

3. **Obsidian（iCloud経由）**

   * 基本的にiCloudが最新化
   * GitHubとの競合は「必ずpull→push」で防ぐ

---

👉 結論
あなたのアイデアは **全然アリで実用的** です。
ただ、iPadで頻繁に編集するなら **Working Copy（iOS用Gitクライアント）** を使った方がGit操作がかなり楽になります。

---

「GitHub Webエディタだけ」だと手軽ですが機能は最低限。
一方で **Working Copy** を使うと、iPadだけで「PCに近いGit運用」が可能になります。

---

## 📱 Working Copyとは？

* iOS専用のGitクライアントアプリ
* GitHubやGitLabなどと直接連携可能
* **Clone / Pull / Push / Commit** が全部iPadで完結
* ファイル編集は内蔵エディタ or 他アプリ（Obsidian, Textastic, IA Writer など）と連携可能

---

## ✅ できること

1. **リポジトリのクローン**

   * GitHubにあるVaultをiPadにCloneしてローカルに保持

2. **変更検知と差分表示**

   * どのファイルを編集したか、差分をハイライト表示

3. **コミットとPush**

   * iPadでMarkdownを編集 → Commit → GitHubにPush

4. **他アプリと連携**

   * iOSの「ファイル」アプリ経由でObsidian Vaultとして認識させられる
   * つまり **Working Copy × Obsidian iOS版** で、PCなしでも「Git連携したObsidian」が完成

---

## ⚖️ GitHub Webエディタとの比較

| 機能        | GitHub Webエディタ                 | Working Copy     |
| --------- | ------------------------------ | ---------------- |
| 編集        | ブラウザで1ファイルずつ                   | 複数ファイル、外部エディタ連携  |
| Commit    | 可能                             | 可能               |
| Push/Pull | Pushは自動（保存＝commit＋push）、Pull不可 | Pull/Push両方可能    |
| オフライン対応   | なし                             | あり（再接続時にpushできる） |
| 競合解決      | できない                           | できる（差分マージあり）     |
| 使いやすさ     | 超簡単                            | 学習コスト少しあり        |

---

## 💡 運用イメージ

* 普段：**PC（Cursor + Obsidian + GitHub）**
* 出先で軽く修正：**GitHub Webエディタ**
* 出先でも本格的に使いたい：**Working Copy + Obsidian iOS版**

  * Obsidianで編集 → Working CopyでCommit & Push

---

👉 まとめると：

* **Webエディタ** = 最低限の編集（簡単、でもPull不可）
* **Working Copy** = iPadを「第2のPC」にするレベルでGit運用可能

