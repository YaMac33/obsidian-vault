[[N13 Drive appDataFolder + GitHub Pages + PKCE で素早く作る — iPad向けPDFメモ（Canvas＋JSON保存）実装手順]]

ジャンル・カテゴリ: ハウツー
[[# 目次
- ## はじめに
- ## 前提（準備）
- ## Quickstart — 最短で動かす手順
- ## Cursorの主要機能と使い方（実践）
- ## Webアプリ開発ワークフロー（フロント／バック／統合）
- ## 実用プロンプト例（そのままコピペ可）
- ## テスト・デバッグ・品質管理
- ## よくある落とし穴と対策
- ## 補足 — 手順で不足しがちな項目
- ## 参考・公式ドキュメント
]]
## はじめに
- Cursorは「自然言語でコード生成・編集ができるAIコードエディタ（VS Code系）」で、コードベースの理解やマルチファイル編集、自然言語リライトなどを提供します。Cursorを使えば、Webアプリのボイラープレート生成、ルーティングやAPIの雛形作成、コンポーネント生成などが速くなります。 [oai_citation:0‡Cursor](https://cursor.com/?utm_source=chatgpt.com)
## 前提（準備）
### 必要なもの
- Cursorアカウント（公式サイトでサインアップ）。有料プランが必要な機能もあるため、用途に合わせて確認。 [oai_citation:1‡Cursor](https://cursor.com/?utm_source=chatgpt.com)
- ローカルにNode.js／npm（またはDeno等）、git、開発用エディタ環境
- プロジェクト用のリポジトリ（GitHub/GitLabなど）
### 推奨設定
- Cursorにプロジェクトルートを読み込ませる（コードベースインデックス化）
- テスト実行環境（Jest, Vitest, Playwrightなど）を用意
- （セキュリティ）Cursorのプライバシー設定を確認（コードの送信ポリシー等）。 [oai_citation:2‡Cursor](https://cursor.com/features?utm_source=chatgpt.com)
## Quickstart — 最短で動かす手順
- 1. Cursorをインストールして開く（デスクトップアプリ／拡張を使用）。
- 2. 既存リポジトリをクローンしてCursorで開く（または新規作成）。
- 3. ターミナルで `npm init` → `npm install`（例: React + Vite）を実行。
- 4. CursorのAIパネルを開き、目的を一文で伝える（例：「React + ViteでシンプルなTODOアプリを作るファイル構成と初期コンポーネントを生成して」）。
- 5. Cursorの提案をコードに取り込む。動かなければ「テストを書いて」「実行コマンドを教えて」といった形で反復。 [oai_citation:3‡Cursor](https://docs.cursor.com/en/guides/tutorials/web-development?utm_source=chatgpt.com)
## Cursorの主要機能と使い方（実践）
### 主要ショートカット（作業効率UP）
- `Ctrl+K`：ファイル内でのコード生成（選択範囲に対する生成）。  
- `Ctrl+L`：AIチャットパネルを開く（Normal / Interpreter モード切替）。  
- `Ctrl+I`：マルチファイル生成／編集コマンド。 [oai_citation:4‡Cursor - Community Forum](https://forum.cursor.com/t/understanding-cursors-ai-feature/7204?utm_source=chatgpt.com)
### モードの違い
- Normalモード：自然言語→コードのやり取り（一般的な生成）。  
- Interpreterモード：実行可能なPythonスクリプト等を生成し、ローカルで実行しながら結果を反映する（実行型タスクで便利）。 [oai_citation:5‡Cursor - Community Forum](https://forum.cursor.com/t/understanding-cursors-ai-feature/7204?utm_source=chatgpt.com)
### コード生成のコツ
- タスクを小さく切る（例：「ユーザー登録API」「フロントのフォーム」など）。  
- コードの前提（ライブラリ、フレームワーク、言語、スタイルガイド）を明示する。  
- 既存のコードや設計文書を参照させると一貫した生成が得られる（Cursorはコードベースを理解可能）。 [oai_citation:6‡Cursor](https://cursor.com/?utm_source=chatgpt.com)
## Webアプリ開発ワークフロー（フロント／バック／統合）
### フロントエンド（例：React）
- 1. プロジェクト初期化（Vite/Next）。  
- 2. ページごとにコンポーネントを生成（Cursorに「Header, TodoList, TodoItem, AddTodoFormを作成して」と指示）。  
- 3. スタイルルールを指定（Tailwind/Emotion等）。  
- 4. ビジュアル確認 → 修正を繰り返す（Cursorの「このコンポーネントをレスポンシブにして」等）。 [oai_citation:7‡Cursor](https://docs.cursor.com/en/guides/tutorials/web-development?utm_source=chatgpt.com)
### バックエンド（例：Express / Fastify / Next API / Firebase）
- 1. APIルーティング雛形を生成（CRUD、認証ミドルウェア）。  
- 2. DBスキーマ（Prisma / TypeORM / SQL）を生成・マイグレーション。  
- 3. エラーハンドリングと検証（Zod等）を追加。  
- 4. Cursorに「このエンドポイントのユニットテストを書いて」と頼むとテストコードも生成可能。 [oai_citation:8‡DataCamp](https://www.datacamp.com/tutorial/cursor-ai-code-editor?utm_source=chatgpt.com)
### 統合（CI/CD・デプロイ）
- 1. GitHub Actions等のワークフローを生成（Cursorに `generate github actions workflow for node` と指示）。  
- 2. ステージングで自動テスト→本番デプロイの流れを確立。  
## 実用プロンプト例（そのままコピペ可）
### 1) 新規プロジェクト作成（React + Vite）

```
Generate a Vite + React TypeScript project structure with a Todo app skeleton.

Include: package.json scripts (dev, build, start), src/main.tsx, src/App.tsx, src/components/TodoList.tsx, TodoItem.tsx, AddTodoForm.tsx, and a simple in-memory state management. Add comments explaining each file.
```

### 2) APIエンドポイント（Express）

```
Create an Express.js TypeScript router for /api/todos with endpoints: GET /api/todos, POST /api/todos, PUT /api/todos/:id, DELETE /api/todos/:id.

Use zod for input validation and include unit tests using Jest. Return JSON with { success: boolean, data?: any, error?: string } format.
```

### 3) テストを先に書く（良い習慣）
```
Write Jest tests for the Todos API: test creating, listing, updating, and deleting todos. Mock DB layer and ensure validation errors return 400.
```

- いずれのプロンプトも「TypeScriptで」「使用するライブラリは〇〇」など細かく指定するほど良いコードが返る。
## テスト・デバッグ・品質管理
- テストファースト推奨：Cursorでテストを生成させ、テストが通るまで反復。これによりAIの生成ミスが見つけやすくなります。 [oai_citation:9‡Builder.io](https://www.builder.io/blog/cursor-tips?utm_source=chatgpt.com)
- 自動静的解析（ESLint/TypeScript）と型チェックをCIに入れる。  
- AI生成コードは“提案”と捉え、人間が必ずレビューする（特にDBや認証周り）。  
- 最新のツールや拡張（CursorのBugbotなど）で自動的にバグ指摘を受ける運用も検討。 [oai_citation:10‡WIRED](https://www.wired.com/story/cursor-releases-new-ai-tool-for-debugging-code?utm_source=chatgpt.com)
## よくある落とし穴と対策
- 「動くが安全でない」コード：DB操作やファイル処理は特にレビュー必須。  
- 依存ライブラリのバージョン差異：生成後に `npm install` でローカル環境を揃える。  
- Cursorがプロジェクト全体を誤解する場合：READMEや設計ファイルを用意してコンテキストを明確にする。 [oai_citation:11‡Cursor](https://cursor.com/?utm_source=chatgpt.com)
## 補足 — 手順で不足しがちな項目
- 環境変数の管理（.env.example と Secrets管理）。  
- ローカルでの動作確認手順（`npm run dev` の想定ポートやエンドポイント確認）。  
- ロールバック手順（デプロイ失敗時の対応）。  
- セキュリティレビュー（Auth、CORS、CSRF、SQLインジェクション対策）。  
- ライセンスと依存ライブラリのセキュリティスキャン（Snyk等）。  
## 参考・公式ドキュメント
- Cursor公式（ホームページ／概要）。 [oai_citation:12‡Cursor](https://cursor.com/?utm_source=chatgpt.com)
- Cursorドキュメント — Web Developmentガイド。 [oai_citation:13‡Cursor](https://docs.cursor.com/en/guides/tutorials/web-development?utm_source=chatgpt.com)
- Cursorの主要機能（Tab, マルチライン編集など）。 [oai_citation:14‡Cursor](https://cursor.com/features?utm_source=chatgpt.com)
- フォーラム：主要ショートカットやモードの使い方まとめ。 [oai_citation:15‡Cursor - Community Forum](https://forum.cursor.com/t/understanding-cursors-ai-feature/7204?utm_source=chatgpt.com)
- 実践ガイド（チュートリアルまとめ）。 [oai_citation:16‡DataCamp](https://www.datacamp.com/tutorial/cursor-ai-code-editor?utm_source=chatgpt.com)
## 最後に（まとめ）
- Cursorは「自然言語で素早くコード生成→人間がレビュー」の高速反復に向いています。小さなタスクに分け、テストを先に用意し、CIで品質管理を行えば、Webアプリ開発の生産性が大きく上がります。公式ドキュメントやコミュニティ情報を参照しつつ、まずは小さな機能を1つCursorで作ってみることをおすすめします。 [oai_citation:17‡Cursor](https://cursor.com/?utm_source=chatgpt.com)


※この記事はChatGPTの回答を基に作成しています。

[[N15 iPadとGitHub CodespacesでCursorは利用可能？PCなしでWebアプリを開発する方法]]