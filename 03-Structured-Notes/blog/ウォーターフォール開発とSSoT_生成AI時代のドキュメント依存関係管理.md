# ウォーターフォール開発におけるSSoTと生成AI時代のドキュメント依存関係管理

## はじめに

ウォーターフォール型開発や大規模システム開発では、要件定義書・設計書・テスト仕様書など多様なドキュメントの依存関係管理が本質的な課題となります。
近年、生成AIのコンテキスト長拡大や、Claude CodeのようなAIプロダクトとGitHub Actions等の自動化基盤の普及により、「Single Source of Truth（SSoT）」としてのリポジトリ運用や、AIによるドキュメント生成・管理の現実味が増しています。

---

## SSoTとしてのリポジトリ運用と生成AIの役割

- すべての開発成果物（要件定義書・設計書・コード・テスト仕様書等）をMarkdown等の構造化ドキュメントとしてリポジトリに格納し、SSoTとする。
- 生成AI（例：Claude Code）とGitHub Actionsを連携させ、PRやIssueコメントをトリガーにAIが下流成果物を自動生成・提案。
- 生成AIによるドキュメント生成・修正は必ずPull Request経由で反映し、人間がレビュー・承認することで品質担保・説明責任を確保。
- すべての履歴・議論・AIアウトプットがリポジトリ上で一元管理され、トレーサビリティが担保される。

---

## 生成AI時代のドキュメント依存関係管理：理論と実践

### 1. 無限コンテキスト長前提での管理手法

- **ディレクトリ構造による階層管理**  
  ドキュメントを論理的なディレクトリ階層で整理することで、AIが全体を把握しやすくなる。依存関係は物理的な配置（パス）で暗黙的に表現可能。

- **明示的なリンク・メタデータ管理**  
  ZettelkastenやObsidianのように、`[[リンク]]`や`#タグ`を使い依存関係や関連性を明示。依存関係グラフやトレーサビリティマトリクスをMarkdownで記述し、AI・人間双方が参照しやすくする。

- **ディレクトリ＋リンクのハイブリッド運用**  
  大まかな階層・分類はディレクトリ、細かな依存や横断的な関係はリンク・タグで補完。AIが両方の情報を統合的に解釈でき、柔軟性と可読性を両立。

---

## 批判的視点・現実的な課題

- 無限コンテキスト長でも「人間の可読性」「レビューやナレッジ共有のしやすさ」は依然として重要。設計は人間中心で考えるべき。
- 上流ドキュメントの曖昧さや非構造性が高い場合、AIによる自動変換の精度や妥当性には限界がある（人間のレビューが不可欠）。
- AIが生成した成果物の判断根拠や説明責任（Explainability）を明示する仕組みは現状不十分。レビュー時に根拠情報をAIから引き出す仕組みや、説明可能なAI設計が求められる。
- 依存関係が複雑化しすぎると、AIの出力根拠やトレーサビリティの説明が難しくなるリスクもある。
- 法的・契約的要件（証跡管理・改ざん防止等）や運用ガバナンス設計、現場の合意形成・教育コストも無視できない。

---

## 実践的な提案

- **ハイブリッド運用の推奨**  
  基本はディレクトリ階層で大分類、細かな依存はリンク・タグで補完する運用が現実的。
- **依存関係の明示化**  
  重要な依存関係やトレーサビリティはMarkdownの表やリストで明示的に記述し、AI・人間双方が参照しやすくする。
- **自動可視化の活用**  
  Mermaidグラフ等による依存関係の自動可視化で全体像を俯瞰できる仕組みを整備。
- **構造化テンプレート・記述ルールの徹底**  
  ドキュメントの粒度やフォーマットを統一し、AIによる自動変換・生成の精度を高める。
- **レビュー・ガバナンス体制の強化**  
  PRレビューや権限管理、説明責任の明確化など、AI活用時代の新たな運用ルールを設計。

---

## 関連ノート・リンク

- [[ドキュメント依存関係管理と生成AIのコンテキスト長の関係]]
- [[ウォーターフォール開発とSSoT_生成AI活用の可能性]]
- [[Claude_Code_GitHub_Actions_技術とユースケース]]
- [[Zettelkastenと確証バイアス]]
- [[生成AIとグローバルスタンダード]]
- [[組織における生成AI駆動開発普及戦略案]]

---

## タグ

#ウォーターフォール #ssot #生成ai #devops #ドキュメント管理 #依存関係 #zettelkasten #ナレッジ共有 #設計パターン #知的生産


[//begin]: # "Autogenerated link references for markdown compatibility"
[ドキュメント依存関係管理と生成AIのコンテキスト長の関係]: ../../02-Permanent-Notes/%E3%83%89%E3%82%AD%E3%83%A5%E3%83%A1%E3%83%B3%E3%83%88%E4%BE%9D%E5%AD%98%E9%96%A2%E4%BF%82%E7%AE%A1%E7%90%86%E3%81%A8%E7%94%9F%E6%88%90AI%E3%81%AE%E3%82%B3%E3%83%B3%E3%83%86%E3%82%AD%E3%82%B9%E3%83%88%E9%95%B7%E3%81%AE%E9%96%A2%E4%BF%82.md "ドキュメント依存関係管理と生成AIのコンテキスト長の関係"
[ウォーターフォール開発とSSoT_生成AI活用の可能性]: ../../02-Permanent-Notes/%E3%82%A6%E3%82%A9%E3%83%BC%E3%82%BF%E3%83%BC%E3%83%95%E3%82%A9%E3%83%BC%E3%83%AB%E9%96%8B%E7%99%BA%E3%81%A8SSoT_%E7%94%9F%E6%88%90AI%E6%B4%BB%E7%94%A8%E3%81%AE%E5%8F%AF%E8%83%BD%E6%80%A7.md "ウォーターフォール開発におけるSSoTとしてのリポジトリとClaude Codeによる実現可能性"
[Claude_Code_GitHub_Actions_技術とユースケース]: ../../02-Permanent-Notes/Claude_Code_GitHub_Actions_%E6%8A%80%E8%A1%93%E3%81%A8%E3%83%A6%E3%83%BC%E3%82%B9%E3%82%B1%E3%83%BC%E3%82%B9.md "Claude CodeのGitHub Actions連携の技術とユースケース"
[Zettelkastenと確証バイアス]: ../../02-Permanent-Notes/Zettelkasten%E3%81%A8%E7%A2%BA%E8%A8%BC%E3%83%90%E3%82%A4%E3%82%A2%E3%82%B9.md "Zettelkasten運用と確証バイアス"
[生成AIとグローバルスタンダード]: ../../02-Permanent-Notes/%E7%94%9F%E6%88%90AI%E3%81%A8%E3%82%B0%E3%83%AD%E3%83%BC%E3%83%90%E3%83%AB%E3%82%B9%E3%82%BF%E3%83%B3%E3%83%80%E3%83%BC%E3%83%89.md "生成AI活用とグローバルスタンダードの重要性"
[組織における生成AI駆動開発普及戦略案]: ../../02-Permanent-Notes/%E7%B5%84%E7%B9%94%E3%81%AB%E3%81%8A%E3%81%91%E3%82%8B%E7%94%9F%E6%88%90AI%E9%A7%86%E5%8B%95%E9%96%8B%E7%99%BA%E6%99%AE%E5%8F%8A%E6%88%A6%E7%95%A5%E6%A1%88.md "組織における生成AI駆動開発普及戦略案"
[//end]: # "Autogenerated link references"
