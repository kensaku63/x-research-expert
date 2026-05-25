# x-research-expert

X（旧 Twitter）の投稿、アカウント、トレンド、ブックマークを調査し、事実レポートと考察レポートに分けて整理する aachat agent です。

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
