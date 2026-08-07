<div align="right">

[![English](https://img.shields.io/badge/lang-English-2b7489?style=flat-square)](./README.en.md)

</div>

# claude-template

Claude Code で**1 つのプロジェクト**を、ゲート付きスクリプトなしの**素の対話**で進めるための
**GitHub テンプレートリポジトリ**。`pipeline.yaml` も `scripts/run.sh` もない。`CLAUDE.md` が
そのままプロジェクトの生きた仕様になり、依存関係のない作業は Workflow ツールで並列に進めて
マージする。複数プロジェクトは別リポジトリとして並列に走らせるだけでよい（共有状態なし）。

段階的なゲート・無人実行・repair ループが必要なプロジェクトは、代わりに姉妹リポジトリ
`claude-pipeline-template` を使うこと（下記「claude-pipeline-template との違い」参照）。

## ファイル構成
- `CLAUDE.md` — プロジェクト記憶。「What this is」（プロジェクトの目的）・ワークフロー方針・
  Plan Mode/Fable の注意・UI ルール・スタックレシピの参照先をまとめて持つ。書き込むのは実質
  ここだけ（`CLAUDE.md` の「What this is」は最初の対話で Claude が埋める想定）。
- `.claude/settings.json` — スコープ付き権限 + ステータスライン設定（下記参照）。素の
  skip-permissions は使わない。
- `.claude/statusline.sh` — ステータスラインの実体。`jq` があれば使い、無ければモデル名のみを
  簡易抽出（effort は確実な値が取れないときは表示しない＝出さない方が安全という判断）。
- `docs/ui-rules.starter.md` — `~/.claude/rules/ui.md`（全プロジェクト共通の UI 方針ファイル）の
  たたき台。一度コピーして育てるもので、このプロジェクト固有の内容ではない。
- `docs/recipes/*.md` — スタック別の既知の落とし穴（例: Tauri + pnpm デスクトップアプリ）。
  該当するスタックを選んだら Claude が早めに読む。

## マシン全体の一度きり設定（このリポジトリ外）
1. git の co-author 行を全プロジェクトで止める：
   `~/.claude/settings.json` → `{ "includeCoAuthoredBy": false }`
2. 全プロジェクト共通の UI 方針：
   `docs/ui-rules.starter.md` を `~/.claude/rules/ui.md` にコピーして継続的に洗練する。
   `~/.claude/CLAUDE.md` と `~/.claude/rules/` は全プロジェクトで読み込まれる。

## プロジェクトごとの使い方
1. GitHub でこのリポジトリを Template repository に設定（Settings → Template repository）。
2. 新規プロジェクトごとに Use this template → 新リポジトリ → clone。
3. クローンしたディレクトリで Claude Code を起動し、そのまま**一行のざっくりした説明**から
   会話を始める。`CLAUDE.md` を先に手で埋める必要はない — Claude が質問しながら詰めて、
   確定した内容を「What this is」に自分で書き戻す。
4. その最初の対話の中で、Claude はこのプロジェクトに合った道具（MCP/プラグイン/スキル）も
   **一度だけ**提案する（「What this is」がまだプレースホルダーのままの間がトリガー）。採用は
   あなたが判断し、導入もあなた自身が行う — Claude は自動インストールしない。
5. 設計・アーキテクチャで重い判断が要るときは Plan Mode（Shift+Tab）。特に難しい判断なら、
   入る前に手動で `/model fable`、終わったら元のモデルに戻す — Claude Code には特定モデルを
   Plan Mode に自動で紐付ける仕組みが無いため、これは毎回手動（詳細は `CLAUDE.md` > Plan Mode）。
6. 互いに依存しない独立した作業（ファイルを共有せず、順序依存もない複数機能など）は、Claude に
   Workflow ツールでの並列実行（サブエージェント + 機能ごとの git worktree）を頼み、確認できた
   ものから `--no-ff` でマージする。ファイルを共有する作業や順序依存がある作業は素直に逐次で。
7. push は明示的に頼まれない限りしない。ローカル git で完結する。

## モデル / effort 表示
`.claude/statusline.sh` が現在のモデル名（確実に取得可）と、Claude Code が対応していれば
reasoning effort をターミナル下部に常時表示する。effort が JSON に出るかはバージョン依存で
未確証のため、取れないときは黙って省略する（憶測値は出さない）。チャットの返答末尾には
繰り返し表示しない。

## claude-pipeline-template との違い
本リポジトリには `pipeline.yaml` / `scripts/run.sh` / `scripts/gates.sh` / `prompts/` / ゲート
付きステージ / `run.sh auto` の無人実行や repair ループがない。テストをゲートにした無人実行や
段階ごとの機械チェックが必要なプロジェクトは `claude-pipeline-template` から始めること。
