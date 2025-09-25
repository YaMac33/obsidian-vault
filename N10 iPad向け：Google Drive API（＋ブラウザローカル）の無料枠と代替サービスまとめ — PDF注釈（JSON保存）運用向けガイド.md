
[[N09 Canvas重ね方式＋JSON保存で実現するPDFメモビューアー構成案]]

## ジャンル・カテゴリ
- ハウツー（実装・設計ガイド）


[[#要約]]
[[#1) Google Drive API：無料枠・制限と運用上の注意]]
[[#2）iPad（Safari）やブラウザローカル保存の実務注意点]]
[[#3）人気のサービスと「無料枠（代表値）」比較（PDF＋注釈JSON保存を想定）]]
[[#4）iPad向けに現実的な運用パターン（推奨）]]
[[#5）実装チェックリスト（Canvas重ね＋JSON保存、iPad対応）]]
[[#6）よくある落とし穴（＆短い対策）]]
[[#7）簡単な意思決定フローチャート（推奨）]]
[[#8）補足（手順で不足しがちな実装ポイント）]]
[[#参考（主要ソース）]]
[[#最終的な推奨（短く）]]

## 要約
iPadで動く「PDF表示＋Canvas上にメモ（JSONで保存）」をGitHub Pagesで配布する想定なら、**Google Drive APIでユーザ毎にJSONを保存（appDataFolder等）→PDFはDrive/Cloud Storageに置く**構成が現実的です。ただしDrive APIのクォータやiPad（Safari）のブラウザ保存の挙動に注意が必要です。以下で「Drive側の無料枠・制限」「ブラウザ（ローカル）保存の実務上の注意」「人気の代替クラウド／無料枠比較」「実運用向け推奨アーキテクチャと実装チェックリスト」を示します。

## 1) Google Drive API：無料枠・制限と運用上の注意
- **APIクォータはプロジェクト／ユーザー単位で割り当てられ、値は固定ではなくコンソールで確認・管理します。** 実際のリクエスト上限や「requests per 100 seconds / user」等はプロジェクトによって異なるため、**Google Cloud Consoleで自分のプロジェクトのQuotaを必ず確認**してください。 [oai_citation:0‡Google Cloud](https://cloud.google.com/docs/quotas/view-manage?utm_source=chatgpt.com)
- **アップロードに関するユーザー側制限**：Workspaceユーザーなどでは、ユーザーあたりのアップロード上限（例：750GB/日）が適用されます（大きなファイルや大量アップロード時は注意）。 [oai_citation:1‡Google for Developers](https://developers.google.com/workspace/drive/api/guides/limits?utm_source=chatgpt.com)
- **クライアント認証（OAuth）**：ブラウザ（静的サイト）からDriveを操作する場合はOAuth 2.0を用います。公開クライアント（SPA/ブラウザ）ではクライアントシークレットを置かない前提で、**Authorization Code + PKCE（推奨）**など安全なフローを採るのがベストプラクティスです。GitHub Pagesのホストドメインを「Authorized JavaScript origins」に登録するのを忘れないでください。 [oai_citation:2‡Google for Developers](https://developers.google.com/identity/protocols/oauth2/resources/best-practices?utm_source=chatgpt.com)
- **アプリ向け隠し領域（appDataFolder）**：ユーザ単位でアプリ専用に使える非表示フォルダ（`appDataFolder`）があり、注釈JSONの保存先として便利です（ユーザがDrive UIで直接見づらい／誤削除されにくい）。ただしこの領域もユーザのDrive容量を消費します。 [oai_citation:3‡Google for Developers](https://developers.google.com/workspace/drive/api/guides/appdata?utm_source=chatgpt.com)

## 2）iPad（Safari）やブラウザローカル保存の実務注意点
- **localStorage**：容量が非常に小さく、一般的に数MB（概ね ~5MB）が上限です。大量の注釈や画像化したデータ格納には向きません。 [oai_citation:4‡MDNウェブドキュメンツ](https://developer.mozilla.org/en-US/docs/Web/API/Web_Storage_API?utm_source=chatgpt.com)
- **IndexedDB（推奨のブラウザ側ストレージ）**：ブラウザによって保存許容量や挙動がまちまちです。デスクトップChrome系では大きな領域（ディスク容量に依存）を使える一方、**iOS Safariでは「保存データの自動削除（eviction）」や容量の低め設定、さらに「7日間サイト未利用で削除される」等のポリシーがあるため注意が必要**。PWAとしてホーム画面に追加されたアプリは挙動が異なる場合があります。必ず `navigator.storage.estimate()` 等で利用可能量を検査し、重要データはクラウドに同期する運用にしてください。 [oai_citation:5‡WebKit](https://webkit.org/blog/14403/updates-to-storage-policy/?utm_source=chatgpt.com)
- **実務アドバイス**：iPadでのオフライン耐性を求めるならIndexedDBにキャッシュ＋定期的にDrive/Firebase等へ同期、というハイブリッド運用が現実的です。

## 3）人気のサービスと「無料枠（代表値）」比較（PDF＋注釈JSON保存を想定）
> ※数値・条件はサービス側で変わるため、導入前に最新コンソールで要確認。

- **Firebase (Google)**  
  - Firestore（DB, 注釈JSON向け）フリーティア：**Stored data 1 GiB、読み取り50,000/日、書き込み20,000/日** といったフリーレベルがある。小規模同期用途に向く。 [oai_citation:6‡Firebase](https://firebase.google.com/docs/firestore/quotas?utm_source=chatgpt.com)  
  - Firebase Storage（ファイル保存）フリーティア例：**5 GB 無料枠（詳細は料金ページで確認）**。PDF本体保存と注釈JSONを組み合わせるなら使いやすい。 [oai_citation:7‡Firebase](https://firebase.google.com/pricing?utm_source=chatgpt.com)
- **Supabase**  
  - 無料プラン：**データベース500 MB、ファイルストレージ 1 GB、5 GB egress（等）**。ただし無料プロジェクトは非アクティブ時に一時停止等の挙動あり。ファイルサイズ制限（Freeプランではアップロード上限が50 MB等）に注意。 [oai_citation:8‡Supabase](https://supabase.com/pricing?utm_source=chatgpt.com)
- **Cloudflare R2 + Workers**  
  - R2 は「Forever Free」枠があり**約10 GB/月の無料ストレージ＋無料のデータ転出（egressなし）**枠があるプランが提供されている（詳細はプランで確認）。静的PDFホスティング＋WorkersでAPIを作る構成はコスト面で有利。 [oai_citation:9‡Cloudflare](https://www.cloudflare.com/developer-platform/products/r2/?utm_source=chatgpt.com)
- **AWS S3（Free Tier）**  
  - 新規AWSアカウント向けFree Tierで**S3 5 GB（12か月）**の無料枠がある。長期運用では料金発生を想定。 [oai_citation:10‡Amazon Web Services, Inc.](https://aws.amazon.com/free/storage/s3/?utm_source=chatgpt.com)
- **Backblaze B2**  
  - 小規模だと初期の数GB無料（例：最初の10 GBが無料扱いのことがある）という案内がある（詳細は公式で確認）。安価に大容量ストレージを確保したいときの選択肢。 [oai_citation:11‡Backblaze](https://www.backblaze.com/cloud-storage/pricing?utm_source=chatgpt.com)
- **Cloudflare Workers KV（Key-Value）**  
  - 小さなJSONや設定保存ならKVも検討可能。Freeプランで読み出し・書込の無料上限が設定されている（例：1 GBストレージ＋日次読み取り/書き込み上限など）。大量読み書きには向かない点に注意。 [oai_citation:12‡Cloudflare Docs](https://developers.cloudflare.com/kv/platform/pricing/?utm_source=chatgpt.com)

## 4）iPad向けに現実的な運用パターン（推奨）
- **個人利用 / シンプル最短ルート（推奨・小規模）**  
  - GitHub Pages（静的）＋PDF.js（PDF表示）＋Drive API（OAuth : Authorization Code + PKCE）で**各ユーザの注釈JSONを`appDataFolder`へ保存**。PDFはDriveの公開リンクかR2等に置く。利点：ユーザごとにGoogleアカウントで同期でき、実装が比較的シンプル。注意：DriveのAPIクォータ／OAuth設定が必要。 [oai_citation:13‡Google for Developers](https://developers.google.com/workspace/drive/api/guides/appdata?utm_source=chatgpt.com)
- **マルチデバイス／高信頼（同期・検索・バックアップ重視）**  
  - フロント（GitHub Pages）→バックエンド（Firebase/Firestore or Supabase）で注釈を保存、PDFはCloud Storage（Firebase Storage / Cloudflare R2 / S3）に置く。利点：DBで検索やバージョン管理、セキュアなトークン管理が容易。Firestoreの無料枠は限定的だが、小規模なら無料で始められる。 [oai_citation:14‡Firebase](https://firebase.google.com/docs/firestore/quotas?utm_source=chatgpt.com)
- **コストを抑え、配信効率重視**  
  - PDFはCloudflare R2で保存・配信（egress無料メリット）、注釈はWorkers KVやR2のメタファイルに格納。 [oai_citation:15‡Cloudflare](https://www.cloudflare.com/developer-platform/products/r2/?utm_source=chatgpt.com)

## 5）実装チェックリスト（Canvas重ね＋JSON保存、iPad対応）
1. **Google Cloudでプロジェクト作成 → Drive APIを有効化。**（Cloud ConsoleでQuota確認） [oai_citation:16‡Google Cloud](https://cloud.google.com/docs/quotas/view-manage?utm_source=chatgpt.com)  
2. **OAuthクライアント作成（Web application）**：GitHub Pagesのドメインを「Authorized JavaScript origins / redirect URIs」に登録。クライアントシークレットはブラウザに置かない（PKCEなどを利用）。 [oai_citation:17‡Google ヘルプ](https://support.google.com/cloud/answer/15549257?hl=en&utm_source=chatgpt.com)  
3. **認可フロー実装**：Authorization Code + PKCE または Google Identity Services を使用。トークンの取得・期限切れ処理を実装。必要ならサーバレス関数で安全にリフレッシュ処理を行う。 [oai_citation:18‡Google for Developers](https://developers.google.com/identity/oauth2/web/guides/migration-to-gis?utm_source=chatgpt.com)  
4. **Driveへの保存先決定**：注釈は`appDataFolder`（ユーザ単位で隠しフォルダ）か、ユーザが選んだDriveフォルダ（`drive.file`スコープ）に保存。`appDataFolder`はUIから見えにくい利点あり。 [oai_citation:19‡Google for Developers](https://developers.google.com/workspace/drive/api/guides/appdata?utm_source=chatgpt.com)  
5. **PDF表示＋Canvas重ねの実装**：PDF.jsでPDFを描画し、上に透過Canvasを重ねて手書き／テキスト注釈を実装。注釈は座標・色・太さ・ページ番号などをJSON化。  
6. **同期／保存ロジック**：ローカル（IndexedDB）に一時保存 → オンライン復帰時にDrive/Firestore/Supabaseへアップロード。iPadのブラウザ仕様（自動削除）を考慮し、**ローカルはキャッシュ扱い**にする。 [oai_citation:20‡WebKit](https://webkit.org/blog/14403/updates-to-storage-policy/?utm_source=chatgpt.com)  
7. **容量・ファイルサイズ対策**：注釈はベクトル（座標配列）で保存して画像化を避ける。スクリーンショット等を保存するなら外部ストレージ（R2/S3等）を使う。 [oai_citation:21‡Cloudflare](https://www.cloudflare.com/developer-platform/products/r2/?utm_source=chatgpt.com)  
8. **セキュリティ**：注釈に機密情報が含まれる可能性があるなら、クライアント側でAES等で暗号化して保存／送信する方式を検討。  
9. **テスト**：iPad（Safari/Chrome）で実機テスト。PWA化（ホーム画面追加）でIndexedDBの持続性が変わるため、PWA→非PWAで挙動差を確認。 [oai_citation:22‡WebKit](https://webkit.org/blog/14403/updates-to-storage-policy/?utm_source=chatgpt.com)

## 6）よくある落とし穴（＆短い対策）
- **ブラウザのみで完結させたらトークンリフレッシュやCORSで詰まる** → 小さなサーバレス関数（Netlify Functions / Cloudflare Workers）で補助する。  
- **iPadで突然IndexedDBデータが消える** → ローカルはキャッシュ扱いにして、クラウドへの自動同期（定期的・遷移時）を加える。 [oai_citation:23‡WebKit](https://webkit.org/blog/14403/updates-to-storage-policy/?utm_source=chatgpt.com)  
- **Drive APIの短期的な大量リクエストでブロックされる** → バックオフ実装と必要最小限のAPI呼び出しにする。Quotaはコンソールで確認・申請可能。 [oai_citation:24‡Google Cloud](https://cloud.google.com/docs/quotas/view-manage?utm_source=chatgpt.com)

## 7）簡単な意思決定フローチャート（推奨）
- 個人＆少数ユーザで「手早く始めたい」→ **Drive appDataFolder + GitHub Pages + PKCE**。  
- 複数端末で共有＆検索やバックアップが重要 → **Firestore（注釈）＋Storage（PDF）** or **Supabase**。 [oai_citation:25‡Firebase](https://firebase.google.com/docs/firestore/quotas?utm_source=chatgpt.com)  
- コスト・配信効率重視（大量PDF配信）→ **Cloudflare R2（PDF）＋Workers（API）**。 [oai_citation:26‡Cloudflare](https://www.cloudflare.com/developer-platform/products/r2/?utm_source=chatgpt.com)

## 8）補足（手順で不足しがちな実装ポイント）
- **CORS設定**：Drive APIや自分で用意するAPI（Cloudflare Workers等）との間でCORSが必要になるので、事前に設定を検証。  
- **スコープ最小化**：`drive.appdata` や `drive.file` 等、必要最小限のOAuthスコープで権限を限定する。 [oai_citation:27‡Google for Developers](https://developers.google.com/workspace/drive/api/guides/appdata?utm_source=chatgpt.com)  
- **エクスポート機能**：注釈JSONのエクスポート（ユーザが手元に保存できる）を用意しておくと、データ損失時の保険になる。  
- **バージョン管理**：注釈はバージョン（タイムスタンプ）を付けて衝突回避ロジックを入れる。  
- **アクセシビリティ（iPad）**：タッチ操作の線の補正、消しゴムのサイズ、Undo/Redoを用意する。

## 参考（主要ソース）
- Google Drive usage limits（Upload limits等）.  [oai_citation:28‡Google for Developers](https://developers.google.com/workspace/drive/api/guides/limits?utm_source=chatgpt.com)  
- View and manage quotas（Google Cloud ConsoleのQuota確認）.  [oai_citation:29‡Google Cloud](https://cloud.google.com/docs/quotas/view-manage?utm_source=chatgpt.com)  
- OAuth 2.0（クライアント側／PKCE等のベストプラクティス）.  [oai_citation:30‡Google for Developers](https://developers.google.com/identity/protocols/oauth2/resources/best-practices?utm_source=chatgpt.com)  
- appDataFolder（Driveのアプリ専用フォルダ）.  [oai_citation:31‡Google for Developers](https://developers.google.com/workspace/drive/api/guides/appdata?utm_source=chatgpt.com)  
- WebKitのStorageポリシー（iOS/iPadOSの7日ルールなど）.  [oai_citation:32‡WebKit](https://webkit.org/blog/14403/updates-to-storage-policy/?utm_source=chatgpt.com)  
- IndexedDB / localStorage 使用上の注意（容量や挙動）.  [oai_citation:33‡RxDB](https://rxdb.info/articles/indexeddb-max-storage-limit.html?utm_source=chatgpt.com)  
- Firebase（Firestore / Storage）の無料枠・料金ページ.  [oai_citation:34‡Firebase](https://firebase.google.com/docs/firestore/quotas?utm_source=chatgpt.com)  
- Supabase 無料プラン（ストレージ・DBの概要）.  [oai_citation:35‡Supabase](https://supabase.com/pricing?utm_source=chatgpt.com)  
- Cloudflare R2（Forever Free / 10GBなど）.  [oai_citation:36‡Cloudflare](https://www.cloudflare.com/developer-platform/products/r2/?utm_source=chatgpt.com)  
- AWS S3 Free Tier（5GB）.  [oai_citation:37‡Amazon Web Services, Inc.](https://aws.amazon.com/free/storage/s3/?utm_source=chatgpt.com)  
- Backblaze B2（無料枠案内・価格比較）.  [oai_citation:38‡Backblaze](https://www.backblaze.com/cloud-storage/pricing?utm_source=chatgpt.com)

## 最終的な推奨（短く）
- **まずはDrive appDataFolder + PKCEでプロトタイプを作る**（iPad上の認証・同期挙動を早めに検証）。運用で同期トラブルや容量問題が出たら、Firestore / Supabase / Cloudflare R2 のいずれかへ移行するのがスムーズです。 [oai_citation:39‡Google for Developers](https://developers.google.com/workspace/drive/api/guides/appdata?utm_source=chatgpt.com)

※この記事はChatGPTの回答を基に作成しています。

[[N11 Obsidianで目次を作る方法｜初心者向け完全ガイド]]
