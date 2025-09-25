[[N03 GitHub Pagesで(root)やdocs以外のディレクトリを公開する方法]]

## 記事カテゴリ
- ジャンル：ハウツー／技術解説

## はじめに
GitHub Pagesは、GitHubのリポジトリを使って無料でWebサイトをホスティングできるサービスです。一方、Hugo・Gatsby・Next.jsは「静的サイトジェネレータ（SSG）」として知られ、ソースコードから高速で最適化された静的HTMLファイルを生成します。本記事では、それぞれの特徴と関係性を詳しく解説します。

## GitHub Pagesとは
### 概要
- GitHubが提供する無料のホスティングサービス
- Gitリポジトリの内容をそのまま、またはビルド済みファイルとして公開可能
- 独自ドメイン対応、HTTPS標準サポート

### 特徴
- 無料かつシンプル：初心者でも数分でデプロイ可能
- Jekyllを標準サポート（ビルドをGitHub側で自動処理）
- 公開範囲：リポジトリ全体または`/docs`フォルダを指定可能

## 静的サイトジェネレータ（SSG）とは
### 定義
- Markdownやコンポーネントを元に、静的HTMLファイルを生成するツール
- サーバーサイド処理不要で、高速かつセキュアなWebサイトを構築可能

### メリット
- 表示速度が速い（静的HTMLを直接配信）
- セキュリティリスクが低い（サーバー側コード不要）
- CDNとの相性が良く、スケーラビリティに優れる

## Hugo・Gatsby・Next.jsの特徴
### Hugo
- Go言語で実装された超高速ジェネレータ
- ビルドが非常に速く、大規模サイトに向く
- テーマが豊富でシンプルな運用が可能

### Gatsby
- Reactベースのジェネレータ
- GraphQLを用いたデータ取得で高度なカスタマイズが可能
- プラグインエコシステムが充実し、SPAのような動作も実現

### Next.js
- Reactフレームワークとして、SSGとSSR（サーバーサイドレンダリング）の両方をサポート
- ハイブリッド構成（静的生成＋動的ページ）が可能
- API RoutesやISR（Incremental Static Regeneration）など高度な機能を提供

## GitHub PagesとSSGの関係性
### 仕組み
- SSGで生成した静的HTML・CSS・JSをGitHubリポジトリにプッシュ
- GitHub Pagesがそのファイルをホスティング
- Jekyll以外のSSGも、ローカルまたはCI/CDでビルド後に`/public`や`/dist`をアップロードすることで利用可能

### 実装の流れ（例：Hugo＋GitHub Pages）
1. Hugoでプロジェクト作成：`hugo new site myblog`
2. テーマを追加：`git submodule add <テーマURL> themes/<テーマ名>`
3. 記事作成とビルド：`hugo`（`public/`に静的ファイル生成）
4. `public/`をGitHubリポジトリの`gh-pages`ブランチへプッシュ
5. GitHub Pagesの設定でブランチを指定して公開

## どのSSGを選ぶべきか
| ジェネレータ  | 特徴                   | 向いている用途        |
| ------- | -------------------- | -------------- |
| Hugo    | 超高速ビルド、テーマ豊富         | 大規模サイトやブログ     |
| Gatsby  | React＋GraphQL、高度な拡張性 | SPA風のサイトや高度なUI |
| Next.js | SSG＋SSRのハイブリッド、API対応 | 動的機能を含むWebアプリ  |

## 選択と運用のポイント
### ポイント
- **開発体験**：Reactに慣れているならGatsbyやNext.js、そうでなければHugo
- **更新頻度と規模**：小規模ブログならHugo、大規模アプリならNext.js
- **ホスティング先**：GitHub Pages以外にNetlifyやVercelも検討可能
- **自動化**：GitHub Actionsを使うとビルド～デプロイを自動化できる

### 補足例：GitHub Actionsでの自動デプロイ
```yaml
name: Deploy Hugo site to GitHub Pages
on:
  push:
    branches: [main]
jobs:
  build-deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - uses: peaceiris/actions-hugo@v2
        with:
          hugo-version: 'latest'
      - run: hugo
      - uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./public
```

### まとめ
- GitHub Pagesは「ホスティング」、Hugo・Gatsby・Next.jsは「生成ツール」
- SSGで生成 → GitHub Pagesで公開、という組み合わせが主流
- サイト規模や技術スタックに応じて最適なSSGを選択することが重要

※この記事はChatGPTの回答を基に作成しています。

[[N05 文字起こしWebアプリ開発をCursor＋GitHub Pages＋Renderで運用する戦略]]

