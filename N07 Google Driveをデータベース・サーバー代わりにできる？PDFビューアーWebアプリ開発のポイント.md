[[N06 Netlify・Vercel・GitHub Pagesの違いと選び方 — 文字起こしWebアプリ向け比較ガイド]]
## はじめに
今回は「PDFビューアーになるWebアプリを開発した際に、Google Driveをデータベースやサーバーの代わりに利用できるか」という質問に答えます。本記事は**ハウツー系の記事**としてまとめています。ホスティングはGitHub Pagesを想定しています。

## GitHub Pagesの特徴
GitHub Pagesは静的サイトホスティングサービスであり、以下の制限があります。
- HTML、CSS、JavaScriptなど静的ファイルのみ公開可能
- サーバーサイド処理（PHPやNode.jsなど）は実行不可
- 無料でSSL（https）が利用可能
- 独自ドメインの設定も可能

このため、バックエンド的な処理を直接GitHub Pagesで行うことはできません。

## Google Driveを使う方法
### 可能なこと
Google Driveを「ストレージ代わり」に使うことは可能です。
- PDFファイルをGoogle Driveにアップロード
- 公開リンクや共有設定を利用し、URLで直接アクセス
- JavaScriptで埋め込み表示やPDF.jsを使ってレンダリング

### 難しいこと
Google Driveを「データベース」や「APIサーバー」として直接使うことは難しいです。
- DriveはSQLのような検索やレコード管理ができない
- 動的なユーザー認証や権限管理はDrive APIを使う必要がある
- APIを使う場合、アクセストークンの管理や認証フローが必要

## 実現するための構成例
### 基本構成（最小限）
- **GitHub Pages**：アプリ本体（静的ファイル）
- **Google Drive（共有リンク）**：PDFファイルの保存場所
- **JavaScript + PDF.js**：PDFをブラウザ内で表示

### 拡張構成（API連携あり）
- **Google Drive API**：ファイルの一覧取得・ダウンロード
- **Google OAuth認証**：ユーザーごとにアクセス制御
- **外部サーバーレス環境**（例：Netlify Functions, Firebase Functions）
  - アクセストークンの安全な処理
  - Drive APIを経由したPDFファイル提供

補足図（シンプルな構成イメージ）:
```
[GitHub Pages] –(静的Webアプリ)–> [ユーザーのブラウザ]
|
v
[Google Drive 公開リンク] –(PDFデータ)–> [PDF.jsで表示]
```

## 実装ステップ例
1. PDFファイルをGoogle Driveに保存し、共有設定で「リンクを知っている全員」に公開
2. 公開リンクを取得してWebアプリのコードに埋め込み
3. JavaScriptでPDF.jsを読み込み、公開リンクからPDFを取得して表示
4. 認証が必要な場合は、Drive API + OAuth 2.0を導入（ただしGitHub Pages単体では不可、外部Functions必須）

## まとめ
- GitHub Pagesは静的ファイルのみなので「サーバー代わり」にはならない
- Google Driveは「ストレージ代わり」には使えるが「データベース・サーバー代わり」には制約が大きい
- シンプルなビューアならDriveの公開リンク + PDF.jsで実現可能
- ユーザーごとに管理や認証が必要ならAPI + 外部Functionsの利用が現実的

※この記事はChatGPTの回答を基に作成しています。