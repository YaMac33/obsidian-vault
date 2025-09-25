[[W02 Google Drive APIを使ったデプロイ手順（具体的解説）]]

カテゴリ: ハウツー（技術解説）

## 概要
`npm install` は Node.js のパッケージ管理ツールである **npm (Node Package Manager)** が提供するコマンドで、プロジェクトで使うライブラリ（パッケージ）を取得してプロジェクトに配置するための基本コマンドです。  
目的は主に「依存関係のダウンロードと配置・プロジェクトで実行できる状態にする」ことです。簡単に言えば「必要なコード（ライブラリ）をインターネットから取ってきて `node_modules` に置く」処理を行います。

## npm install の代表的な動作パターン
### ローカル（プロジェクト）依存を一括インストール
- コマンド:（プロジェクトルートで）
    npm install
  - 説明: カレントディレクトリの `package.json` と `package-lock.json`（あれば）を参照し、必要な依存関係を `node_modules` にインストールします。`package-lock.json` があれば再現性の高いインストールが行われます（より厳密には `npm ci` がCI向けの決まった手順をとります）。

### 特定パッケージを追加インストール（依存として保存）
- コマンド:
    npm install lodash
    npm install express@4.17.1
  - 説明: 指定したパッケージをダウンロードし `node_modules` に置き、`package.json` の `dependencies` に追記（既定）します。バージョンを指定すればそのバージョンをインストールします。

### 開発専用の依存として追加
- コマンド:
    npm install --save-dev jest
    npm install -D eslint
  - 説明: テストやビルドツールなど、運用時には不要な開発用依存を `devDependencies` に追加します。

### グローバルにインストール
- コマンド:
    npm install -g typescript
  - 説明: システム全体でコマンドとして使いたいツールをグローバル領域にインストールします。Unix 系では権限問題で `sudo` が必要になることがあるため、推奨は `nvm` 等でユーザー管理された Node 環境を使うこと。

## 内部で何が起きているか（簡潔な流れ）
- `package.json` / `package-lock.json` を読み取る
- 依存関係ツリーを解決（どのバージョンを取るか決める）
- npm レジストリからパッケージの tarball をダウンロード
- ダウンロードしたパッケージを `node_modules/<package>` に展開
- `node_modules/.bin` にコマンドのシンボリックリンクを作る（CLI を使えるようにする）
- 必要に応じて `package-lock.json` を更新
- 各パッケージのライフサイクルスクリプト（`preinstall`/`install`/`postinstall`）を実行

## よく使うオプション・コマンド
- `npm install` / `npm i` — package.json に基づくインストール
- `npm install <pkg>` — 指定パッケージをインストールして package.json に保存
- `-D` / `--save-dev` — devDependencies に保存
- `-g` / `--global` — グローバルにインストール
- `--no-save` — package.json を更新しない
- `-E` / `--save-exact` — package.json に正確なバージョンで保存
- `npm ci` — CI用。`package-lock.json` に厳格に従ってクリーンインストール（`node_modules` を削除してから速やかにインストール）
- `npm audit` / `npm audit fix` — セキュリティ脆弱性の検出と自動修正の試行
- `npm cache clean --force` — キャッシュ問題の解決時に利用（注意して使う）

## バージョン指定の書き方（覚えておくと便利）
- `1.2.3` — 完全一致（固定）
- `^1.2.3` — マイナーバージョンの互換範囲で最新（例: 1.x）
- `~1.2.3` — パッチバージョンの互換範囲（例: 1.2.x）
- `latest` / 省略 — レジストリの最新互換バージョン（意図せぬアップデートを避けたいなら `--save-exact` を検討）

## 実践フロー（ゼロからプロジェクトを作る場合）
- Node.js をインストール（推奨: nvm でバージョン管理）
- 新規プロジェクト作成:
    mkdir my-app
    cd my-app
    npm init -y
- 依存を追加:
    npm install express
- 開発依存を追加:
    npm install -D nodemon
- 実行例（package.json の scripts を使う）:
    // package.json の scripts に "start": "node index.js" を追加しておく
    npm run start

（補足: CI では `npm ci`、ローカル開発で依存を追加する際は `npm install` を使うのが一般的）

## トラブルシューティング・注意点
- **権限エラー（globalインストール）**: グローバルで `EACCES` 等が出たら `sudo` でごまかすのではなく、`nvm` を使ってユーザーレベルで Node を管理することを推奨します。
- **大量の `node_modules` サイズ**: モノリポや多数パッケージで肥大化します。不要な依存は削る、`--production` や `--omit=dev` を使う。
- **依存の衝突**: 依存解決でバージョン衝突が起きることがあります。`package-lock.json` を確認・再生成、`npm dedupe` を試す。
- **再現性が重要な環境（CI）**: `npm ci` を使用し、`package-lock.json` を必ずコミットする。
- **脆弱性対応**: `npm audit` で検出、`npm audit fix` を慎重に利用（自動修正で互換性が壊れることがある）。
- **`node_modules` は Git に入れない**: `.gitignore` に `node_modules/` を追加し、`package.json` と `package-lock.json` を管理する。

## 補足: package.json の最小例
    {
        "name": "my-app",
        "version": "1.0.0",
        "scripts": {
            "start": "node index.js",
            "test": "jest"
        },
        "dependencies": {
            "express": "^4.17.1"
        },
        "devDependencies": {
            "jest": "^29.0.0"
        }
    }

## 追加で必要になりがちな「手順で不足しているもの」の補足
- **Node / npm のバージョン確認**:
    node -v
    npm -v
- **nvm の導入**（推奨）でバージョン管理と権限問題の回避
- **CI 用設定**: `npm ci` を使うためには `package-lock.json` を必ずコミットしておく
- **パッケージ公開時の注意**: 誤って `.env` 等の秘密をパッケージに含めない（`.npmignore` の確認）
- **ローカルで実行する CLI の確認**: `node_modules/.bin` がスクリプト実行時に参照される点を理解しておく

## 結論（短くまとめ）
`npm install` は Node.js 環境で必要なライブラリを取得してプロジェクトを動かせる状態にするための基本コマンドです。単にコマンドを叩くだけでなく、`package.json` / `package-lock.json` の役割、ローカルとグローバルの違い、`npm ci` や `--save-dev` 等のオプションの使い分け、そして権限や再現性に関する注意点を理解すると、トラブルが格段に減ります。

---


※この記事はChatGPTの回答を基に作成しています。