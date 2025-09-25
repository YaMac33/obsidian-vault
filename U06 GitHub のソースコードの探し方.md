[[U05 script src=URLとmarkdownInput.value = sampleText; preview.innerHTML = marked.parse(sampleText);の解説]]

GitHub のソースコードを効率よく探す／ブラウズするのに役立つサービスやツールをいくつか紹介するね。目的（コード例を探す／言語横断検索／自分のリポジトリ内検索など）によって使い分けるといい。

---

## オススメのツール・サイト

|ツール名|特長／使いどころ|
|---|---|
|**GitHub Code Search**|GitHub 自身の検索機能。正規表現やブール演算子、リポジトリ／言語フィルタなどが使えて、最新の公開リポジトリを対象に高速で検索可能。 ([GitHub](https://github.com/features/code-search?utm_source=chatgpt.com "Code Search"))|
|**Sourcegraph (Code Search)**|大規模なコードベースを横断的に検索するのに強い。複数のコードホストをサポートしており、コードインテリジェンス（シンボル検索、参照先・定義の追跡など）機能もある。 ([Sourcegraph](https://sourcegraph.com/code-search?utm_source=chatgpt.com "Code Search"))|
|**grep.app**|GitHub 上の多くのリポジトリを対象に、ファイル／パス名やコードのフラグメントを素早く探せる検索エンジン。軽めに使いたいときに便利。 ([Grep](https://grep.app/?utm_source=chatgpt.com "Code Search \| Grep by Vercel"))|
|**Code Search Guide のツール一覧**|色んな種類のコード検索ツールをまとめてくれてて、「どのツールがどの用途向きか」が見られるので、自分に合ったものを選ぶ指標になる。 ([コード検索ガイド](https://codesearchguide.org/tools?utm_source=chatgpt.com "Code search tools - Code search guide"))|
|**Hound**|自分や組織の複数リポジトリを対象にインデックスを作り、非常に速く検索できる静的検索サーバー形式。オープンソースで、自ホストも可能。 ([GitHub](https://github.com/hound-search/hound?utm_source=chatgpt.com "hound-search/hound: Lightning fast code searching made ..."))|

---

もしよければ、**日本語コード**が多く含まれるリポジトリや、特定の言語（例：Python／Go／JavaScript）を中心に探したい場合に特化したものも紹介できる。どうする？

日本語コメントや日本語ドキュメントを含むコード、または特定言語に特化して探す場合に便利な方法やツールをまとめたよ。

---

## 日本語コードや日本語コメントを探す

|方法／ツール|ポイント|
|---|---|
|**GitHub Code Search のクエリ活用**|例：「`言語処理` language:Python`」や「`日本語 site:github.com`」など、日本語キーワード＋`language:`フィルタを使う。|
|**grep.app**|キーワードに日本語を入れて検索すると、コメントやREADMEに含まれる日本語を簡単に見つけられる。|
|**Sourcegraph**|組織内コードでも日本語コメントやメソッド名を横断検索できる。正規表現も使える。|
|**Awesome 日本語リソース系リポジトリ**|「awesome-japanese」や「awesome-jp」などを検索すると、日本語対応や日本向けのライブラリまとめが見つかる。|

---

## 言語別の特化サイト・検索テクニック

| 言語                        | 方法・サービス                                                                                                                                        |
| ------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------- |
| **Python**                | - [PyPI](https://pypi.org/)でライブラリ名を見てGitHubへ飛ぶ- GitHub で「`topic:python`」「`language:Python`」を指定- [grep.app](https://grep.app/)で「`def 関数名`」などを検索 |
| **JavaScript/TypeScript** | - [npmjs.com](https://www.npmjs.com/)で人気パッケージからGitHubリンクを辿る- GitHubで「`topic:javascript`」「`language:TypeScript`」                                |
| **Go (Golang)**           | - [pkg.go.dev](https://pkg.go.dev/)でパッケージを探してGitHubへ- Sourcegraphで`language:go`検索                                                              |
| **Rust**                  | - [crates.io](https://crates.io/)→GitHubリンク- GitHubで「`language:Rust`」「`topic:rust`」                                                            |
| **Java**                  | - [Maven Central](https://search.maven.org/)経由でGitHubリポジトリへ- GitHub Code Searchで`language:Java`＋キーワード                                          |

---

## その他ヒント

- **検索演算子**：
    
    - `in:file`（ファイル内だけ）、`in:readme`（READMEだけ）、`path:`（パス名検索）
        
    - `stars:>100`（スター数フィルタ）、`pushed:>2024-01-01`（更新日の指定）
        
- **ブックマーク**：頻繁に使う検索クエリはブラウザに保存すると効率的。
    
- **日本語OSSまとめサイト**：QiitaやZennで「日本語 OSS」「日本語 対応ライブラリ」などを検索すると、有志がまとめた記事がヒットしやすい。
    

これらを組み合わせると、日本語情報が豊富なコードや目的の言語向けリポジトリをスムーズに見つけられるよ。

[[U07 JavaScript と TypeScript]]
