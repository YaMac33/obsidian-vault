[[F01 ローカルPCはサーバー代わりになる？GitHub Codespacesとバックエンド開発の効率化]]

ジャンル・カテゴリ：ハウツー

[[#はじめに]]
[[#Dockerでローカルサーバーを立ち上げる手順]]
[[#ngrokで一時公開URLを作る手順]]
[[#組み合わせの具体例]]
[[#注意点と補足]]
[[#まとめ]]

## はじめに
iPadや外部端末からローカルPC上のサーバーを確認したい場合、Dockerで環境を統一しつつ、ngrokで一時的に公開する方法が便利です。  
これにより、PCを常時公開サーバーとして稼働させる必要がなくなり、安全かつ簡単にモバイル端末からアクセスできます。

## Dockerでローカルサーバーを立ち上げる手順

### 1. Dockerのインストール
- Windows、macOS、Linuxの公式サイトからDocker Desktopをインストール
- インストール後、ターミナルで以下を実行して動作確認

```
docker –version
```

### 2. Dockerfileの作成
- プロジェクトルートに `Dockerfile` を作成
- 例：Node.jsアプリの場合
```
FROM node:20-alpine
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3000
CMD [“node”, “index.js”]
```

### 3. Dockerイメージのビルドとコンテナ起動
```
docker build -t my-app .
docker run -p 3000:3000 my-app
```

- `-p 3000:3000` はホストPCの3000番ポートをコンテナにマッピング

## ngrokで一時公開URLを作る手順

### 1. ngrokのインストール
- [公式サイト](https://ngrok.com/)からダウンロードしてインストール
- 認証トークンを設定
```
ngrok authtoken YOUR_AUTH_TOKEN
```

### 2. ngrokでポートを公開
```
ngrok http 3000
```

- これで `https://xxxxx.ngrok.io` のような一時URLが生成され、iPadやスマホからアクセス可能

### 3. 接続確認
- iPadのブラウザでngrokが発行したURLにアクセス
- Docker上のローカルサーバーに接続できることを確認

## 組み合わせの具体例
- DockerでNode.jsサーバーを3000番ポートで起動
- ngrokでそのポートを公開
- iPadや他のPCで `https://xxxxx.ngrok.io` にアクセスして動作確認
- サーバーやngrokは不要になったら停止可能（`Ctrl+C`）

## 注意点と補足
- **セキュリティ**：ngrokはインターネット経由でアクセスできるため、認証やCORSの設定を確認
- **セッション制限**：無料プランではURLがランダムで、再起動すると変わる
- **リソース**：Dockerコンテナとngrokを同時に動かすためPCの負荷は多少増える

## まとめ
- Dockerで環境を統一しつつ、ngrokで一時公開する方法は、iPadなどからローカルサーバーを確認する際に便利
- 常時公開サーバーを用意せずに、簡易的に外部アクセスが可能
- 無料枠でも十分テスト・開発用途で活用できる

※この記事はChatGPTの回答を基に作成しています。

[[F03 iPadやiPhoneでDockerを使ったローカルサーバーの立ち上げ方法]]