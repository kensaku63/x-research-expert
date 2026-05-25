# x-research-expert

X（旧 Twitter）の投稿、アカウント、トレンド、ブックマークを調査し、事実レポートと考察レポートに分けて整理する aachat agent です。

## 特徴

### bird CLI と X API v2 を使い分けるハイブリッド検索

X 調査では、`bird` CLI と X API v2 を目的に応じて使い分けます。
広く探索したい検索、投稿・スレッド・返信・トレンド確認には bird を使い、投稿量カウントや再現性のある取得、API fallback が必要な場面では X API v2 を使います。

単一の取得手段に依存せず、調査目的、認証状態、API の制約に合わせて柔軟に検索できます。

### エージェントが扱いやすい構造化検索インターフェース

検索は API や CLI を直接叩くのではなく、専用 script でラップしています。
エージェントは複雑な X 検索演算子を毎回手書きせず、`keywords`、`any-of`、`exclude`、`hashtags`、`from-accounts`、`period` などの構造化フィールドで検索意図を渡せます。

script 側で bird 用・X API 用のクエリに変換し、取得結果を JSON として正規化するため、後続のレポート作成や反復調査にそのまま使いやすい形で扱えます。

### ノイズを落として、調査に使える投稿を残す

取得した raw response をそのまま使わず、script 側でスパム・重複・自動投稿・品質の低い投稿をフィルタします。
短すぎる本文、リンクだけの投稿、ハッシュタグ過多、検索語に一致しない投稿、スパム辞書に当たる投稿、近似重複、低品質 author、エンゲージメント異常などを除外対象にします。

除外理由は `excluded_summary` として残るため、なぜ採用されなかったのかも後から確認できます。
ノイズが多い場合は、次に追加すべき除外語候補も返します。

### 安定したアウトプットを作るレポートテンプレート

取得結果は、専用のファクトレポートテンプレートに沿って Markdown 化します。
調査条件、検索ログ、検索品質メモ、次に試す検索候補、投稿グループ、代表投稿、取得できなかった情報、失敗した検索を同じ形式で整理します。

ファクトレポートでは、仮説・施策・推測を混ぜず、観察可能な事実だけをまとめます。
代表投稿 ID や検索 ID も安定させるため、後続の考察レポートや追加調査から根拠を参照しやすくなります。

### aachat loop で検索戦略を更新しながら深掘りする

単発検索で終わらず、aachat の `/loop` 機能を使って複数ターンの反復調査を進められます。
各ターンで既存の検索ログ、失敗した検索、ノイズ過多だった条件、`search_quality`、`next_query_candidates`、考察レポートの次論点を読み直し、前回と異なる検索戦略を立てます。

広く探す discovery と、絞り込んで確かめる validation を交互に行い、必要に応じて反証検索も入れることで、同じ検索の繰り返しではなく、調査目的に向かって段階的に深掘りできます。

## 必要な env

値は `aachat/.state/env.toml` もしくは Infisical に設定してください。

- `AUTH_TOKEN`: ブラウザで X にログインし、開発者ツールの Cookie から `auth_token` を取得します。
- `CT0`: 同じ Cookie から `ct0` を取得します。
- `X_BEARER_TOKEN`: X Developer Portal で対象 app / project の bearer token を取得します。

## 構成

- `identity.md`: エージェントの役割、skill の使い分け、調査時の行動方針。
- `environment.yaml`: 依存パッケージと env 名の宣言。
- `memory/`: session 間で引き継ぐ状態や未完了の調査方針を置く場所。
- `knowledge/`: 長期参照する X 調査の運用知識や公開リファレンスを置く場所。
- `.agents/skills/x-search-plan`: 調査目的を検索戦略に分解し、必要に応じて反復調査を立ち上げる skill。
- `.agents/skills/x-search`: X の一次取得 script と正規化ロジック。
- `.agents/skills/x-search-report`: 取得 JSON から事実レポートを作る skill。
- `.agents/skills/x-search-insight`: 事実レポートから考察・仮説・次論点を作る skill。
- `.agents/skills/x-bookmark-deep-research`: X ブックマークを起点に深掘り調査する skill。
