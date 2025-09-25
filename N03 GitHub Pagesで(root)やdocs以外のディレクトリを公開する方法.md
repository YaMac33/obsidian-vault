[[N02 GitHub Pagesで文字数から読了時間を表示する方法]]
## 記事概要
- カテゴリ：ハウツー（How-to）
- 対象読者：GitHub Pages初心者〜中級者
- 目的：任意のディレクトリ（例：`/public`や`/site`など）をGitHub Pagesで公開する手順を解説

## GitHub Pagesの基本仕様
GitHub Pagesでは、以下の場所のみがデフォルトで公開対象として選択できます。
- ルートディレクトリ（`/`）
- `/docs`ディレクトリ
- 任意のブランチの`/`（例：`gh-pages`ブランチ）

これ以外のディレクトリ（例：`/public`や`/site`）を直接指定することはできません。そのため、公開したいディレクトリをPagesが参照する場所に「移動」または「ビルド」する必要があります。

## 公開するためのアプローチ
### 1. ディレクトリを`/docs`へ移動する
- 公開したいフォルダを`/docs`にリネームまたはコピー
- リポジトリ設定：  
  - **Settings → Pages → Source** で「`main` branch」→「`/docs` folder」を選択  
  - 保存後、`https://ユーザー名.github.io/リポジトリ名` でアクセス可能

### 2. ルートディレクトリへ移動する
- 公開したいディレクトリの中身をリポジトリのルートに移動
- Pagesのソースを「`main` branch → `/`」に変更
- 既存ファイルとの衝突に注意（READMEや他のコードとの整合性）

### 3. 別ブランチを公開用に使う
- 公開用のブランチ（例：`gh-pages`）を作成し、公開したいディレクトリをそのブランチのルートに配置
- 設定でソースを「`gh-pages` branch → `/`」に変更
- 自動デプロイにはGitHub Actionsを活用する

### 4. GitHub Actionsで自動ビルド&デプロイを行う
特にビルドステップが必要なフレームワーク（例：React, Vue, Svelte）では、GitHub Actionsを使って公開ディレクトリを自動的に`gh-pages`ブランチまたは`/docs`へ出力する方法が一般的です。

#### サンプルWorkflow（`/.github/workflows/deploy.yml`）
```yaml
name: Deploy to GitHub Pages
on:
  push:
    branches:
      - main
jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Install dependencies
        run: npm install
      - name: Build site
        run: npm run build
      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./public
```
- publish_dirに公開したいフォルダを指定（例：./public）
- デプロイ後、指定したディレクトリがgh-pagesブランチのルートとして公開される

### 補足：公開URLの確認
- 設定後、GitHub Pagesの設定画面に表示されるURLを確認
- キャッシュの影響で反映に数分かかる場合がある
- 公開できないときは以下をチェック  
- ブランチとフォルダの指定が正しいか
- ファイル名が小文字・大文字を正しく区別しているか
- リポジトリが「Public」設定になっているか

### まとめ
- GitHub Pagesは/または/docsのみを直接公開可能
- 他ディレクトリを公開するには：  
- /docsまたはルートへ移動する
- 公開用ブランチを使用する
- GitHub Actionsなどで自動デプロイする
- 小規模サイトは/docs利用が簡単、大規模・ビルド必要なプロジェクトはGitHub Actions＋gh-pagesブランチが便利

※この記事はChatGPTの回答を基に作成しています。

[[N04 GitHub PagesとHugo・Gatsby・Next.jsの関係性と特徴を徹底解説]]

