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
  SKILL.md                 # ワークフロー本体(ゲート、9ステップ、Red Flags、ドメイン対応表)
  templates/
    proposal.md            # WHY
    spec.md                # WHAT(デルタ形式含む)
    design.md              # HOW(条件付き)
    tasks.md               # 実行チェックリスト
```

利用方法: superpowers プラグインとして読み込むか、`skills/spec-driven-workflow/` を
`~/.claude/skills/` にコピーすれば単体スキルとしても動作する(依存なし・自己完結)。
