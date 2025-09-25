# Cursor＋Obsidian＋GitHub＋NotebookLM 連携・活用ガイド

## 1. 各ツールの役割と特徴

- **Cursor**  
  AIコーディング支援エディタ。コード補完やAIチャット、Git連携が強力。  
- **Obsidian**  
  ローカルで動作するノートアプリ。Markdownで知識管理・リンク・検索が得意。  
- **GitHub**  
  バージョン管理・バックアップ・チーム共有の基盤。  
- **NotebookLM**  
  GoogleのAIノートブック。自分のノートや資料をAIが要約・検索・Q&Aしてくれる。

---

## 2. 連携の全体像

1. **知識・アイデア・設計・メモ**  
   → ObsidianでMarkdown管理（双方向リンク・タグで整理）

2. **コード作成・編集**  
   → CursorでObsidianのノートやコードを直接編集  
   → AI補完やチャットで効率化

3. **バージョン管理・共有**  
   → GitHubでObsidianノートやコードを一元管理  
   → PC/iPad/他端末で同期

4. **AIによる知識活用**  
   → NotebookLMにObsidianノートやGitHubリポジトリをアップロード  
   → AIがノート全体を横断検索・要約・Q&A

---

## 3. 具体的な効率化ワークフロー

### A. 知識とコードの一元管理

- ObsidianのVault（ノートフォルダ）をGitHubリポジトリ化
- CursorでVaultを開き、ノートとコードを同時編集
- コード断片や設計メモをMarkdownで残し、双方向リンクで整理

### B. バージョン管理と多端末同期

- 変更は`git add/commit/push`でGitHubに反映
- iPadや他PCでもGitHub経由で最新ノート・コードを取得
- Obsidian GitプラグインやWorking Copy（iOS）で運用も可

### C. NotebookLMによるAI知識活用

- Obsidian VaultやGitHubリポジトリをNotebookLMにアップロード
- 「このプロジェクトの設計思想は？」「XXの使い方は？」など自然言語でAIに質問
- ノート・コード・設計書を横断的にAIが要約・抽出

---

## 4. 効率化Tips

- **ノートとコードを分けず、同じVaultで管理**  
  → 設計・議論・実装がシームレス
- **Obsidianのリンク・タグを活用**  
  → NotebookLMでのAI検索精度も向上
- **定期的にGitHubへPush**  
  → バックアップ・履歴管理・多端末同期が自動化
- **NotebookLMで「プロジェクト全体の要約」や「過去議論の抽出」**  
  → 情報探索・ナレッジ共有が爆速

---

## 5. まとめ：最も効率的な活用法

- **Obsidianで知識・設計・議論をMarkdown管理**
- **CursorでノートとコードをAI活用しながら編集**
- **GitHubでバージョン管理・多端末同期・チーム共有**
- **NotebookLMでAIによる知識検索・要約・Q&Aを実現**

この4ツールを連携させることで、  
「知識の蓄積・検索・実装・共有」が一気通貫で効率化されます。

---

### 【参考：運用イメージ図】


