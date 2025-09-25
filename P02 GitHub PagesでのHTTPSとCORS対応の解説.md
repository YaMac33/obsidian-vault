
[[P01 GitHub Pagesで作る便利ツール集Webサイトのアイデア]]

ジャンル・カテゴリ：ハウツー（Web開発・セキュリティ）

[[#はじめに]]
[[#HTTPSとは]]
[[#CORSとは]]
[[#GitHub-PagesでのHTTPSとCORSの関係]]
[[#外部API利用時の注意点]]
[[#まとめ]]

## はじめに
GitHub Pagesは無料でWebサイトを公開できる便利なサービスですが、HTTPSが標準で適用されるため、外部APIを利用する場合にCORS（Cross-Origin Resource Sharing）に関する制約に注意が必要です。  
ここでは、HTTPSとCORSの関係、そして外部APIを安全かつ正しく使う方法を解説します。

## HTTPSとは
- HTTPSは「Hypertext Transfer Protocol Secure」の略
- 通信内容を暗号化してやり取りするプロトコル
- URLは「https://」から始まる
- GitHub Pagesでは自動的にHTTPSが有効化される

### HTTPSのメリット
- データの盗聴や改ざんを防止
- ブラウザからの「安全ではないサイト」との警告を回避
- API通信も暗号化されるので安全

## CORSとは
- CORSは「Cross-Origin Resource Sharing」の略
- 別ドメイン（オリジン）のリソースにアクセスする際のブラウザ制約
- 同一オリジンポリシーによる制限を回避するための仕組み
- 外部API側が明示的に許可していないと、ブラウザから直接アクセスできない

### CORSエラーの例
- GitHub PagesのHTTPSサイトからHTTPのAPIを呼び出すと「Mixed Content」エラー
- HTTPS同士でも、API側がCORSを許可していないと「CORS policy: No 'Access-Control-Allow-Origin'」のエラーが出る

## GitHub PagesでのHTTPSとCORSの関係
- GitHub PagesはHTTPS標準なので、サイト側は常に安全な通信
- 外部APIがHTTPSに対応していない場合、ブラウザは通信をブロック
- さらにAPIがCORS設定をしていない場合、直接JavaScriptからアクセスできない

### 対策
- **APIがCORSを許可しているか確認**  
  → `Access-Control-Allow-Origin` が自分のサイトまたは `*` になっているか
- **プロキシサーバーを利用**  
  → 自分のサーバーやサーバーレス関数経由でAPIを呼び出す
- **HTTPS対応のAPIを使用**  
  → GitHub PagesはHTTPSなので、HTTP APIは使えない場合が多い

## 外部API利用時の注意点
- APIドキュメントでCORS対応の有無を確認
- APIキーをブラウザ側で扱う場合は安全性に注意（可能ならサーバー側で隠す）
- ブラウザからの直接呼び出しでは制約が多いため、必要に応じてサーバーレス関数やCloudflare Workersなどを経由

## まとめ
- GitHub PagesはHTTPSが標準で安全だが、その分CORS制約が厳しくなる
- 外部APIを使う場合はCORS対応の確認が必須
- HTTP APIは使えず、HTTPS対応APIかプロキシ経由での利用が基本
- 安全で快適なWebサービスを作るために、HTTPSとCORSの関係を理解して設計することが重要

※この記事はChatGPTの回答を基に作成しています。


[[P03 GitHub Pages用便利ツール集Webサイトのリポジトリ名案]]