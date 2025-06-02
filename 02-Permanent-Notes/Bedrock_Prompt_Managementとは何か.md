# Amazon Bedrock Prompt Management とは何か

## 定義・概要
Amazon BedrockのPrompt Managementは、生成AIの基盤モデル（Foundation Models, FMs）に対するプロンプト（指示文）を作成・保存・バージョン管理・共有できるAWS公式の管理機能です。プロンプトの品質向上や再利用、組織的なナレッジ共有を効率化します。

## 主な機能・特徴
- **プロンプトの作成・保存・再利用**：独自プロンプトを作成し、異なるワークフローやプロジェクトで再利用可能。
- **バージョン管理**：プロンプトごとにバージョンを管理し、過去の内容へのロールバックやA/Bテストが容易。
- **構造化プロンプト**：システムインストラクションやツール設定など、複雑な指示体系を分割して管理可能。
- **共有・コラボレーション**：AWSアカウント内でプロンプトを共有し、チームや組織全体でナレッジ資産として活用。
- **API統合**：Converse APIやInvokeModel API経由でプロンプトARNを指定して呼び出し可能。
- **セキュリティ・権限管理**：IAM等のAWS標準の権限管理・監査ログに対応。

## IAMによる権限制御（Action/Condition）
Bedrock Prompt Managementでは、IAMポリシーで細かな権限制御が可能です。

### 代表的なAction例
- `bedrock:CreatePrompt`：プロンプトの新規作成
- `bedrock:CreatePromptVersion`：プロンプトのバージョン作成
- `bedrock:DeletePrompt`：プロンプトやそのバージョンの削除
- `bedrock:GetPrompt`：プロンプトの取得
- `bedrock:ListPrompts`：プロンプト一覧の取得
- `bedrock:UpdatePrompt`：既存プロンプトの更新

> これらのActionは、`Resource`要素でARN指定が可能なものと、ワイルドカード（`*`）指定が必要なものがあります。詳細は[Actions defined by Amazon Bedrock](https://docs.aws.amazon.com/service-authorization/latest/reference/list_amazonbedrock.html)を参照。

### 代表的なCondition例
- `aws:RequestTag/${TagKey}`：リクエスト時のタグに基づく制御
- `aws:TagKeys`：許可されたタグキーに基づく制御
- `bedrock:ResourceTag/${TagKey}`：リソースに付与されたタグに基づく制御

> これらのConditionキーを使うことで、タグベースのアクセス制御（ABAC）が可能です。

#### ポリシー例
```json
{
  "Effect": "Allow",
  "Action": [
    "bedrock:CreatePrompt",
    "bedrock:UpdatePrompt",
    "bedrock:DeletePrompt"
  ],
  "Resource": "*",
  "Condition": {
    "StringEquals": {
      "aws:RequestTag/Project": "GenAI"
    }
  }
}
```

## 活用例
- 複数の生成AIワークフローで共通プロンプトを標準化
- バージョン管理を活かしたプロンプトのA/Bテストや最適化
- プロンプトのセントラルリポジトリ化による全社的なナレッジ共有
- 生成AIエージェントの複雑な指示設計・管理
- API経由での動的なプロンプト切り替え

## 注意点・ベストプラクティス
- **品質管理**：共有・再利用が容易な反面、不適切なプロンプトが流通しないようレビューや権限設定が重要。
- **バージョン混在リスク**：古いバージョンの誤用を防ぐため、運用ポリシーやCI/CDへの組み込みを推奨。
- **API連携時の管理責任**：ARN指定で外部から呼び出す場合、常に最新・正しいバージョンを参照しているか監視が必要。

## 他サービスとの比較
| 項目                | Bedrock Prompt Management | 他社プロンプト管理サービス |
|---------------------|--------------------------|--------------------------|
| AWS公式統合         | ◎（API連携・IAM等）      | △（AWS標準ではない）     |
| バージョン管理      | ◎                        | サービスにより異なる     |
| 構造化プロンプト    | ◎                        | サービスにより異なる     |
| セキュリティ・権限  | ◎                        | サービスにより異なる     |
| 共有・コラボ        | ◎                        | サービスにより異なる     |

## 反証・異論となる視点
- AWS外のサービスやOSSプロンプト管理ツールの方が柔軟なカスタマイズや多様なモデル対応を持つ場合もある。
- AWSアカウントやIAM設計が複雑な場合、権限管理や運用負荷が増すリスクもある。

---

### 関連ノート・リンク案
- [[IAMポリシーの種類と設計原則]]
- [[Bedrockのリソースレベル権限制御とタグ活用]]
- [[プロンプトエンジニアリングのベストプラクティス]]
- [[Bedrock Flowsによるワークフロー自動化]]
- [[Anthropic ClaudeとBedrockの連携事例]]

---

#aws #bedrock #promptmanagement #iam #権限管理 #プロンプト管理 #生成AI
