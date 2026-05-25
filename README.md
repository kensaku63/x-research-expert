# x-research-expert

X（旧 Twitter）の投稿、アカウント、トレンド、ブックマークを調査し、事実レポートと考察レポートに分けて整理する aachat agent です。

## 必要な env

この agent は `environment.yaml` の `config.env[]` で、script が参照する環境変数名だけを宣言します。値や provider ref は repo に置かず、ローカルの aachat env provider に設定してください。

- `AUTH_TOKEN`: `bird` CLI 用の X cookie auth token。`CT0` と組で使います。
- `CT0`: `bird` CLI 用の X csrf cookie。`AUTH_TOKEN` と組で使います。
- `X_BEARER_TOKEN`: X API v2 用 bearer token。Recent Search、counts、lookup、API fallback で使います。

未設定でも session 起動自体は止まりません。取得 script は `limitations[]` と `next_human_actions[]` に不足情報を返すため、必要な env を設定して `aachat up` 後に再実行してください。

## 使い方

```bash
aachat agent clone kensaku63/x-research-expert --name x-research-expert
aachat project assign <project> --agent x-research-expert
aachat session run x-research-expert --project <project> "<調査したい内容>"
```

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
