[[I03 サーバーレスFunctionsとAPI Gatewayとは？]]


## はじめに
「サーバーレスFunctions」や「API Gateway」を導入する際、まず気になるのが **コスト** です。幸い、主要なクラウドサービスでは無料枠（Free Tier）が用意されており、小規模開発や学習目的なら十分活用できます。本記事では、無料で利用できる代表的なサービスを紹介します。

## 無料で使えるサーバーレスFunctions
### 1. AWS Lambda
- **無料枠**：  
  - 月間 100万リクエスト  
  - 月間 400,000GB-秒（実行時間×メモリ使用量）  
- **特徴**：クラウドサービスの代表格。AWS API Gatewayと組み合わせて利用可能。

### 2. Google Cloud Functions
- **無料枠**：  
  - 月間 200万リクエスト  
  - 400,000 GB-秒の実行時間  
- **特徴**：GCPエコシステムとの統合が強力。学習・実験用途にも向く。

### 3. Azure Functions
- **無料枠**：  
  - 月間 100万リクエスト  
  - 400,000 GB-秒のリソース利用  
- **特徴**：マイクロソフト環境との親和性が高い。Office 365やTeams連携に便利。

### 4. Cloudflare Workers
- **無料枠**：  
  - 1日 100,000 リクエスト  
  - KVストレージも一定範囲で無料  
- **特徴**：CDN上で超高速に動作。グローバル展開が容易。

### 5. Vercel Functions / Netlify Functions
- **無料枠（例：Vercel）**：  
  - 月間 125時間分のサーバーレス実行  
- **無料枠（例：Netlify）**：  
  - 月間 125,000件のリクエスト  
- **特徴**：フロントエンド開発者向け。GitHubとの連携でデプロイが簡単。

## 無料で使えるAPI Gateway
### 1. AWS API Gateway
- **無料枠**：  
  - 月間 100万リクエスト（REST API / HTTP APIともに対象）  
- **特徴**：Lambdaとの組み合わせで最も利用される構成。

### 2. Google Cloud Endpoints / API Gateway
- **無料枠**：  
  - GCP全体の無料枠に含まれる（少量のリクエストであれば無償）  
- **特徴**：GCP環境に適したAPI管理が可能。

### 3. Azure API Management
- **無料枠**：  
  - 「Developer」プランが低コストで提供されており、小規模開発用に利用可能  
- **特徴**：Azure Functionsと組み合わせやすい。

### 4. Cloudflare Workers + API Routes
- **無料枠**：  
  - Workersと一緒にAPIルーティングを実現可能  
- **特徴**：軽量で高速。API Gateway的な利用も可能。

## 学習・小規模開発におすすめの組み合わせ
- **完全無料で始めたい場合**  
  - Cloudflare Workers + KV  
  - Netlify Functions または Vercel Functions  

- **本格的に学習・商用利用を見据える場合**  
  - AWS Lambda + API Gateway（業界標準）  
  - Google Cloud Functions + Endpoints  

## まとめ
- **サーバーレスFunctions** は主要クラウドすべてに無料枠あり  
- **API Gateway** もAWSをはじめ複数サービスで無料枠が提供  
- 小規模開発や学習なら「Cloudflare」「Vercel」「Netlify」が簡単  
- 商用利用を見据えるなら「AWS」や「GCP」が安心  

無料枠をうまく活用すれば、サーバー管理なしで高度なアプリをコストゼロで開発できます。


※この記事はChatGPTの回答を基に作成しています。


[[I05 GitHub Pagesでファイルアップロードフォームを作成した場合の保存先について]]
