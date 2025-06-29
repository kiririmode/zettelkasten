# MCP×Redmine連携によるAIプロジェクト支援

## 概要・目的
MCP（Model Context Protocol）を介してRedmineのチケット情報をLLMに提供することで、AIによるプロジェクト管理・意思決定支援・業務自動化を実現する。

## 参考

- [MCPを使ってAIスクラムマスターを作ろう！](https://note.com/guchey/n/nee276b7d1c51)

## 連携アーキテクチャ
- MCPサーバがRedmine APIと連携し、チケット情報（課題、進捗、担当者、コメント等）をResource/ToolとしてLLMに提供
- LLMはPromptを通じて「進捗を要約して」「リスクを抽出して」などの指示を受け、Redmineの情報を活用した応答や提案を生成

## 主要ユースケース
- チケット進捗の自動要約・レポート生成
- タスク割り当て・リマインドの自動提案
- リスク抽出・優先順位付けのAI支援
- チケット新規作成・更新の自動化

## 実装・運用上のポイント
- ResourceとしてRedmineのチケット情報を構造化して提供
- ToolとしてRedmine APIのチケット作成・更新・検索等を実装
- Promptで「Redmineの進捗を要約して」「未対応チケットをリストアップして」などの指示を柔軟に設計
- セキュリティ・権限管理、情報鮮度・データ整合性に配慮

## Redmine用MCPサーバの実装・技術動向

### 概要
Redmine用MCPサーバは、RedmineのAPIをMCPプロトコルでラップし、AIや各種クライアントからRedmine操作を自動化・拡張するための中継サーバです。Docker化されたOSS実装やNode.jsによる自作例も登場しています。

### 代表的なOSS・実装例
- [kunikada/mcp-redmine-docker](https://github.com/kunikada/mcp-redmine-docker)：Redmine APIをMCP経由で操作できるDockerコンテナ型サーバ。チケット作成・検索・更新などをHTTP/SSEで実行可能。
- Node.jsによる自作MCPサーバ例：ローカルで動作し、Redmine API連携も拡張可能。

### API設計・仕様例
- RESTful設計＋SSE（Server-Sent Events）対応
- 主要エンドポイント例：
  - `/tickets/create`（チケット作成）
  - `/tickets/search`（検索）
  - `/tickets/update`（更新）
  - `/projects/list`（プロジェクト一覧）
- 認証：RedmineのAPIキーやOAuth2に対応

### ユースケース
- LLMクライアント（例：Claude、Cursor）から自然言語でRedmineのタスク管理・検索・更新を自動化
- SlackやメールMCPサーバと連携し、Redmineイベントを他サービスに転送
- ローカルエディタからRedmine issue管理を一括実行

### 技術的課題・考慮点
- Redmine APIのバージョン・認証方式の変化への追従
- 認証情報の安全管理・HTTPS必須
- コマンドマッピングやパラメータ変換の柔軟性
- SSE/WebSocketによるリアルタイム通知の設計

### 参考OSS・記事
- https://github.com/kunikada/mcp-redmine-docker
- https://qiita.com/megmogmog1965/items/8bbcb6fb856ab2eba446
- https://zenn.dev/danimal141/articles/63f9652c43a4b4

---

## 反証・課題・今後の展望
- 誤操作や機密情報漏洩リスク（AIによる自動更新時の監査・承認フローが必要）
- 複雑な運用ルールや非構造的なコメント情報の扱い
- Redmine APIの制約や、システム間のデータ整合性
- 今後は他サービス連携や社内ナレッジ統合も視野に入る

## 関連ノート・リンク案
- [[MCPとは何か]]
- [[MCPサーバ_Resource_Prompt_Tool]]
- [[外部API連携とAIの実用化]]

## タグ
#ai/mcp #redmine #プロジェクト管理 #ai連携 #業務自動化 #ai/agents


[//begin]: # "Autogenerated link references for markdown compatibility"
[MCPとは何か]: MCP%E3%81%A8%E3%81%AF%E4%BD%95%E3%81%8B.md "MCP（Model Context Protocol）とは何か"
[MCPサーバ_Resource_Prompt_Tool]: MCP%E3%82%B5%E3%83%BC%E3%83%90_Resource_Prompt_Tool.md "MCPサーバのResource・Prompt・Toolとは何か"
[//end]: # "Autogenerated link references"
