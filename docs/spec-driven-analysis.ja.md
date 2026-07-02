# 仕様駆動開発 3実装の比較分析と `spec-driven-workflow` スキルの設計

対象リポジトリ:

- **spec-kit** (GitHub) — SDD方法論の原典的実装。`/speckit.*` コマンド群
- **OpenSpec** (Fission-AI) — 変更(change)単位のアーティファクト誘導型ワークフロー(OPSX)
- **superpowers** (obra / Prime Radiant) — エージェントの行動そのものを形成するスキル集

## 1. 各実装の哲学

### spec-kit — 「仕様が主、コードは従」

コードではなく仕様をソース・オブ・トゥルースとする「権力の逆転」(`spec-driven.md`)。
パイプラインは constitution(憲法=プロジェクト原則)→ specify → clarify → plan → tasks
→ analyze → implement。特徴:

- **spec テンプレート**: ユーザーストーリーを P1/P2/P3 で優先度付けし、**各ストーリーが独立してテスト・デリバリー可能**(P1単体でMVP)であることを強制
- **`[NEEDS CLARIFICATION]` マーカー**: 推測で埋めず、明示的に「未確定」を記録
- **clarify コマンド**: 最大5問の的を絞った質問で曖昧さを解消し、回答を仕様に書き戻す
- **analyze コマンド**: spec / plan / tasks 間の整合性を非破壊的に検査
- **constitution**: 全アーティファクトが従うべき上位原則を文書化

弱点: フィーチャー単位のパイプラインが比較的ウォーターフォール的で、儀式が重い。ブラウンフィールド(既存システムの変更)の扱いが薄い。

### OpenSpec — 「フェーズではなくアクション」

哲学は `fluid not rigid / iterative not waterfall / built for brownfield`。

- **change ディレクトリ**: 1変更 = `changes/<name>/{proposal, specs/, design, tasks}`
- **アーティファクト依存グラフ**(schema.yaml): 依存は「次に何が可能か」を示すもので、順序の強制ではない
- **デルタ仕様**: 既存の生きた仕様(living specs)に対し `ADDED / MODIFIED / REMOVED / RENAMED Requirements` で差分だけを書く。**archive 時にデルタを本体へマージ**し、living specs が常に現在の合意を表す
- **要求フォーマット**: `### Requirement:`(SHALL/MUST)+ `#### Scenario:`(WHEN/THEN)。全要求に最低1シナリオ
- **explore モード**: 成果物を作らない思考パートナー
- **config.yaml**: プロジェクト文脈・アーティファクト別ルールの注入

弱点: 実行時のエージェント行動(検証の徹底、質問の仕方など)への規律は薄い。

### superpowers — 「スキルは散文ではなく、エージェント行動を形成するコード」

ワークフローを CLI ではなく行動規範として実装。

- **brainstorming**: 実装前の HARD-GATE(設計承認まで一切実装禁止)。「単純すぎて設計不要」をアンチパターンとして名指し。**質問は1回に1つ**、選択肢形式優先。**2〜3案+推奨案**の提示。人間の承認ゲート
- **writing-plans**: 「コンテキストゼロの実行者」を想定した計画。正確なパス・コマンド・コード必須。**プレースホルダ禁止**(TBD/「適切に処理」は計画の失敗)。セルフレビュー(仕様カバレッジ/プレースホルダ走査/型整合)
- **verification-before-completion**: 「新鮮な検証証拠なしに完了を主張しない」(Iron Law)
- **Red Flags 表**: エージェントが陥る合理化を先回りして潰す

弱点: 開発作業に特化しており、仕様の永続化(living specs)の概念がない。

## 2. アーティファクト連鎖の対応表

| 問い | spec-kit | OpenSpec | superpowers |
|------|----------|----------|-------------|
| 原則 | constitution.md | config.yaml (context/rules) | (CLAUDE.md 相当) |
| WHY | spec.md 冒頭 | proposal.md | brainstorming の対話 |
| WHAT | spec.md (user stories + FR) | specs/**(要求+シナリオ、デルタ) | design doc(specs/) |
| 曖昧さ解消 | /clarify(≤5問) | explore / 検証 | 1問ずつの質問 |
| HOW | plan.md + research/data-model/contracts | design.md(条件付き) | plans/(詳細計画) |
| タスク | tasks.md([P] 並列、ストーリー別) | tasks.md(チェックボックス) | 計画内の bite-sized steps |
| 実行 | /implement | /opsx:apply | executing-plans / subagent-driven |
| 検証 | /analyze + checklist | /opsx:verify | verification-before-completion |
| 完了処理 | (ブランチマージ) | /opsx:archive(デルタ→本仕様) | finishing-a-development-branch |

## 3. 統合スキル `spec-driven-workflow` への採用要素

汎用化の方針: 「コード」を「実行(execution)」に、「テスト」を「観測可能なシナリオ検証」に一般化することで、開発以外(プロジェクトマネジメント、イベント運営、コンテンツ制作、業務プロセス変更)にも適用可能にした。

| 採用元 | 採用した要素 |
|--------|--------------|
| spec-kit | 優先度スライス(P1=単独で成立するMVP)、`[NEEDS CLARIFICATION]`、clarify(≤5問)、constitution → `principles.md`、クロスアーティファクト整合性検査 |
| OpenSpec | change ディレクトリ規約、proposal/spec/design/tasks の4アーティファクト、**デルタ仕様と living specs へのマージ(archive)**、「フェーズではなくアクション」、design.md の条件付き作成、要求+WHEN/THEN シナリオ形式 |
| superpowers | HARD-GATE(承認前の実行禁止)、「単純すぎる」アンチパターン、1問ずつの質問、2〜3案+推奨、プレースホルダ禁止、コンテキストゼロの実行者想定、証拠に基づく検証(タスク消化ではなく仕様シナリオに対して検証)、Red Flags 表 |

### ドメイン一般化の対応

| アーティファクト | 開発 | PM/運用 | コンテンツ |
|------------------|------|---------|-----------|
| proposal | 機能提案 | プロジェクト憲章 | キャンペーンブリーフ |
| spec | 要求+受入シナリオ | 成果物+完了基準 | オーディエンス+成功指標 |
| design | 技術設計 | 体制・ベンダー選定 | クリエイティブ方針 |
| tasks | 実装チェックリスト | オーナー付きWBS | 制作・公開チェックリスト |
| verify | テスト・デモ | ステークホルダー受入 | 指標 vs 成功基準 |
| living spec | システム仕様 | 業務手順書・ランブック | ブランド・チャネル仕様 |

## 4. スキルの構成

```
skills/spec-driven-workflow/
  SKILL.md                 # ワークフロー本体(2モード、ゲート、9ステップ、複数日実行、Red Flags、ドメイン対応表)
  templates/
    brief.md               # WHY+WHAT 1枚(ライトモード: 日常の複数日タスク向け)
    proposal.md            # WHY(フルモード)
    spec.md                # WHAT(デルタ形式含む、フルモード)
    design.md              # HOW(条件付き)
    tasks.md               # フォールバック: Backlog.md 不使用時の実行チェックリスト
    log.md                 # フォールバック: Backlog.md 不使用時の作業ジャーナル
  references/
    backlog-md.md          # Backlog.md CLI とワークフロー概念の対応表・コマンド例
```

### 複数日タスクへの対応(ライトモード)

日常の非開発業務で「複数日かかるタスクを分解して実行する」用途向けに、2モード制を採用:

- **ライトモード**(日常業務のデフォルト): proposal+spec を1枚の brief に圧縮。承認ゲートは1回。完了基準は WHEN/THEN シナリオのまま維持
- **セッション再開プロトコル**: セッション開始時にマイルストーン・タスク・ノートを読んで状況を復元し、要約を提示してから着手。終了時は必ず「やったこと・決定・次の一手(コールドスタート可能な粒度)」を記録
- **待ち状態管理**: 外部への依頼・承認待ちはフォローアップ日付きで追跡し、待ちの間は次の非ブロックタスクへ進む
- **タスクの粒度**: 1セッション(半日以内)で完了するサイズに分割。外部待ちを発生させるタスクは最優先で先行実行

### ドキュメント階層(憲法より上位の層)

spec-kit の constitution(憲法)は「制約」のみを扱い、その上位の「方向」が存在しなかった。
本スキルでは常設ドキュメントを**方向(direction)の連鎖**と**制約(constraints)の柵**の2系統に分離:

```
方向(なぜ→何のため)                 制約(常に従う)
North Star(ミッション/ビジョン)      Principles(憲法: 品質基準、
  ↓ を正当化根拠とする                  予算上限、決裁権限、コンプラ)
Goals: <期間>(四半期/年の目標)             │
  ↓ を正当化根拠とする                      │
Change(brief / proposal+spec)   ←── 全レイヤーに適用
  ↓ を分解したものが
Milestone → 子タスク(Backlog.md)
```

運用ルール:

- **トレーサビリティ**: brief/proposal は必ず `Serves:` 行で「どの目標に効くか」を明記。目標に紐付かない仕事は `obligation`(法定義務など)か `maintenance`(維持作業)と明示するか、着手前に人間へ問い返す(「孤児作業」の防止)
- **優先順位**: North Star > Goals > Principles > 変更ドキュメント > タスク。ただし常設ドキュメント間の矛盾はエージェントが勝者を選ばず、必ず人間に確認
- **上位ドキュメント自体の改訂も同じワークフローで行う**: 期初の Goals 改訂や Principles の修正は、それ自体が1つの変更(ライトモード: brief → 承認 → doc 更新)。他の作業のついでに書き換えることは禁止
- **Goals 改訂時は進行中作業を再チェック**: セッション開始時に、進行中マイルストーンの brief が現行 Goals にまだトレースできるか確認し、孤児化していれば報告
- 全レイヤーはオプショナル(不在が実害を生んだ時に初めて作る)。ただし作った以上は拘束力を持つ

### タスク管理バックエンド: Backlog.md

実行状態の管理は [Backlog.md](https://github.com/MrLesk/Backlog.md)(`backlog` CLI)を前提とする。
規約は「**マイルストーン=親タスク、細かいタスク=子タスク**」:

| スキルの概念 | Backlog.md 上の実体 |
|---|---|
| principles / brief / proposal / spec / design | `backlog doc`(変更ごとに `changes/<name>` フォルダ) |
| マイルストーン(デリバリー単位のスライス) | 親タスク(ラベル `milestone`)。brief の WHEN/THEN 完了基準を AC として持つ |
| 細分化タスク | `-p <milestone-id>` の子タスク(1セッションで完了するサイズ、AC 必須) |
| 順序・ブロック | `--dep` |
| 設計判断 | `backlog decision create` |
| セッションジャーナル | マイルストーン親タスクの Implementation Notes(`--append-notes`、`NEXT:` 行必須) |
| 外部待ち | ラベル `waiting` + ノートにフォローアップ日 |
| 検証 | 証拠を確認してから `--check-ac`(一括チェック禁止) |
| クローズ | `--final-summary` → `-s Done` → `backlog task archive` / `backlog cleanup` |

ネイティブの `backlog milestone` 機能ではなく親タスクをマイルストーンとする理由:
親タスクは AC・ステータス・ノート・依存関係を持てるため、検証可能でジャーナルの置き場所にもなる
(ネイティブ milestone は説明のみ)。ネイティブ milestone は複数変更を横断するリリース束ねに使ってもよいが、本ワークフローの必須要素ではない。
タスクファイルの直接編集は禁止(ID・メタデータ破損防止)。常に CLI 経由で操作する。
Backlog.md が使えない環境では `templates/tasks.md` + `templates/log.md` のプレーンファイル運用にフォールバック(ワークフロー自体は同一)。

利用方法: superpowers プラグインとして読み込むか、`skills/spec-driven-workflow/` を
`~/.claude/skills/` にコピーすれば単体スキルとしても動作する(依存なし・自己完結)。
