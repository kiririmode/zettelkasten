# バクラクモノレポAI Coding環境整備とRoo/Claude Code活用事例

## 概要

LayerXバクラク事業部の@upamune氏による、モノレポ環境でのAI Coding Agent活用における課題と解決策、及びRoo Code/Claude Codeの具体的な活用事例の発表内容をまとめたもの。モノレポ特有の課題として開発者間のルール相違、ディレクトリ構造の複雑性、コンテキストサイズの肥大化があげられ、これらに対する実践的な解決手法が示されている。

## モノレポでのAI Coding Agent活用における3つの主要課題

### 課題1: 開発者間でのAI Coding Agentルール要求の相違

モノレポでは同一リポジトリ内でも開発者のチーム、技術スタック、開発規約が異なるため、AI Codingルールの統一が困難になる。不要なルールがコンテキストウィンドウを圧迫し、ノイズとなる問題が発生する。

解決策として「Rules Bank」手法を採用し、`.clinerules/`と`.roo/rules/`をgit管理外にして、必要なルールを別ディレクトリから選択的に導入する仕組みを構築した。さらに、github.com/upamune/airuleツールを開発し、fuzzy finderによるルール選択を効率化している。

### 課題2: ワークスペースルートディレクトリの不統一

モノレポでは開発者が特定サービスディレクトリからワークスペースを開くことが多く、この場合AI Coding Agentがルールファイルを認識できない問題が生じる。

github.com/upamune/airulesyncツールを開発し、設定ファイルベースでルールディレクトリを各サービスディレクトリに自動同期する仕組みを実装した。

### 課題3: コンテキストサイズの肥大化

バクラクバックエンドは420万行、19,959ファイルの大規模モノレポであり、AI Codingでコンテキストウィンドウがすぐに埋まってしまう課題がある。適切なファイルへの到達も困難になる。

現在も完全な解決策は見出せていないが、Gemini 2.5 ProやRoo CodeのIntelligent Context Condensing機能の活用を検討している。Claude Codeではripgrepを内蔵したRAG非依存の検索手法が効果的に機能している。

## AI Coding Agent非依存のコンテキスト整備

### 重要性

コンテキスト整備は特定のAI Coding Agentに依存しない資産となり、プロジェクト固有の規約理解や既存コードとの一貫性確保に不可欠である。不適切なコンテキストは一般的なベストプラクティスに基づく提案しか生成できない。

### 具体的なコンテキスト例

**構造化文書**：ADR（Architecture Decision Records）、Design Doc、SpecをNotionから自動でMarkdown変換するツールを社内開発。技術的意思決定の記録を維持する。

**DBスキーマ情報**：k1low/tblsツールを利用してDBスキーマからドキュメントを自動生成し、ER図、テーブル定義、リレーション情報をMarkdown形式で提供する。

**社内ライブラリドキュメント**：docs/libraries配下に社内ベストプラクティスをMarkdown形式で蓄積。MCPサーバー（Context7、Deep Wiki）を活用して効率的なドキュメント作成を実現する。

## Roo Code活用の特徴と利点

Roo CodeはClineのフォークから独立した開発を行っており、カスタムモード機能とOrchestratorモードが大きな特徴である。開発者は用途に応じた専用モードをYAML形式で定義でき、Orchestratorモードにより大きなタスクを適切なサブタスクに分解して実行できる。

実際の活用例として、ADR/Design Doc Writer、Figma UI Reviewer、Playwright UX Reviewer、Devin Instructorなどの社内カスタムモードを定義し、特定業務に特化したAI支援を実現している。

## Claude Code（Opus 4）の革新的な性能

Claude Sonnet 4/Opus 4の登場により、従来の詳細なルール整備なしでも高品質なアウトプットが得られるようになった。モノレポでの動作も大幅に改善され、曖昧な指示でも90%程度の要求を実現できている。1日$300-400の利用コストで運用中だが、開発者の満足度は他のAI Coding Agentを大きく上回っている。

## 実践的なTips

### AIワークスペースの設計

AIが自由に実験できる専用ディレクトリ（`.gitignore`対象）を設置し、実装計画書（plan.md）の置き場として活用する。

### 定型実装のプレイブック化

「ユーザーにプロダクト利用フラグを追加する」等の頻出パターンを文書化し、Pull Requestのdiffから自動生成することで効率化を図る。

### git worktreeの戦略的活用

特にCLI型Claude Codeにおいて、複数ブランチでの並列セッション実行によりワークフロー効率が大幅に向上する。DockerボリュームのClone機能と組み合わせてデータ分離も実現している。

## 新たな課題と今後の展望

Claude Codeの高性能化により開発速度が加速した結果、コードレビューの負荷が急激に増大している。Copilot Review、CodeRabbit、Greptile等の自動レビューツールの導入を検討中である。

## 批判的視点

AI Coding Agent依存の開発文化は、開発者のコード理解能力や設計思考力の低下を招く可能性がある。特に大規模モノレポでの盲目的なAI依存は、システム全体のアーキテクチャ把握を困難にし、技術的負債の蓄積リスクを高める恐れがある。また、高額な利用コスト（1日$300-400）の持続可能性と、AI生成コードの品質保証体制の確立も重要な課題である。

## 関連ノート

- [[AI活用戦略_コスト削減型vs能力拡大型の企業競争力]]
- [[Claude_Code_メンタルモデル変革_定額使い放題時代の開発哲学]]
- [[VS_Code_Copilot_CustomizationのInstructionsとPrompts]]
- [[Building Effective Agents_Anthropic]]

#vibecoding #roo-code #claude-code #development
