[[G01 ChatGPT回答を「1回答1記事」で自動化する最適な方法🚀]]

## はじめに✨
Obsidianの**Dataviewプラグイン**は、ノートをデータベースのように扱い、柔軟なクエリで情報を抽出・表示できる超強力なツールです🔥。Markdownファイルのメタデータ（YAMLフロントマター）や本文から情報を集計し、表・リスト・タスク管理などを自動生成できます📑。

## Dataviewの特徴🌟
### 1. ノートをデータベース化できる
- YAMLフロントマターやタグを利用して、ノート間の情報を整理可能📂。
- タスク管理、読書ログ、習慣トラッカーなど、様々なデータビューを作成可能📅。

### 2. 表・リスト・カレンダー表示に対応
- `table`・`list`・`task`など複数の表示モードを選択可🖼️。
- Markdown内でダイナミックなビューを作成し、ノートを探す手間を省略🔎。

### 3. クエリ言語で柔軟な抽出
- SQLライクなクエリで条件を指定可能⚡。
- 例：`where`、`sort`、`group by`などを使用して高度なフィルタリング🛠️。

## インストールと有効化方法🛠️
### 1. Dataviewプラグインのインストール
- Obsidianを開き、**設定 → Community Plugins → Browse**を選択🔎。  
- **Dataview**を検索して「Install」をクリック📥。  
- インストール後、**Enable**をオンにする✅。  

### 2. 必要な設定を有効化
- **Allow Reading YAML Frontmatter**をオンにすると、メタデータを認識可能📄。  
- **Inline Fields**を有効にすると、本文内の`key:: value`形式も扱える📌。

## 基本的な使い方📋
### 1. 表示モード別クエリ例
#### 表形式（table）
```dataview
table author as "著者", status as "進捗"
from "Books"
where status != "完了"
sort status asc
```

#### リスト形式（list）
```dataview
list
from "Tasks"
where contains(tags, "重要")
```

#### タスクビュー（task）
```dataview
task
from "Projects"
where !completed
```
### 2. YAMLフロントマターの例
```yaml
---
title: 読書ログ📚
author: 山田太郎
status: 未完了
tags: [読書, 学習]
---
```

### 3. インラインフィールドの利用
- 本文中に `進捗:: 50%` のように書くと、Dataviewでフィールドとして扱える📊。

## 応用テクニック💡
### 1. グループ化と集計
```dataview
table status, length(file.tasks)
from "Projects"
group by status
```
### 2. 日付管理
- `date(today)` を使って今日の日付を基準にフィルタ可能📆。
- 例：`where due < date(today)`。

### 3. タグを活用したダッシュボード
- タグベースで記事やメモを動的に集約し、**ホームノート**を作成📌。  
- 習慣トラッカーや進捗管理に最適✅。

## トラブルシューティング🔧
- 表示されない場合は、**DataviewJSプラグイン**が必要なコードを使用していないか確認。  
- YAMLの書式ミス（インデントやコロン）を修正。  
- Obsidianを再読み込み（`Ctrl + R` または`Cmd + R`）でキャッシュをクリア。  

## おすすめ活用例📈
- **読書管理**：タイトル、著者、進捗を自動一覧化📚。  
- **タスク管理**：未完了タスクを動的抽出✅。  
- **ブログ記事管理**：公開済み・下書き・アイデアを分類📝。  
- **日記や習慣トラッカー**：日付と進捗を可視化📅。  

## まとめ🌟
- DataviewはObsidianをデータベース的に使うための必須プラグイン💎。  
- YAML・インラインフィールド・クエリを駆使して、柔軟な情報管理が可能📂。  
- 表、リスト、タスクビューを組み合わせて、ノートを効率よく整理しよう🚀。  

※この記事はChatGPTの回答を基に作成しています。


[[G03 ObsidianのTemplaterプラグイン徹底解説🧩]]
