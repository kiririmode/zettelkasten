# MCPサーバの起動場所と設定ファイルの関係

## 定義・概要
MCPサーバ（Model Context Protocolサーバ）は、Visual Studio Code（VS Code）環境でAIエージェントや外部ツールと連携する際に利用されるサーバである。その起動場所は、どの設定ファイルにサーバコマンドを記述するかによって変化する。

## 詳細
VS Codeは、MCPサーバの起動コマンドを「ユーザー設定」「リモート設定」「ワークスペース設定」など複数の設定ファイルで指定できる。どの設定ファイルに記述するかによって、サーバが起動する場所（ホストOSかリモート環境か）が決定される。

- **ユーザー設定**（例：`settings.json`）に記述した場合、VS Codeが動作しているホスト（例：WindowsやmacOS）でMCPサーバが起動する。
- **リモート設定**（例：WSL2やSSHリモートの`settings.json`）や**ワークスペース設定**に記述した場合、そのリモート環境（例：WSL2内）でMCPサーバが起動する。

この仕様により、意図しない環境でサーバが起動し、コマンドが見つからない（`ENOENT`）などのエラーが発生することがある。

## 批判的視点・反論
公式ドキュメントでは仕様として明記されているが、ユーザー体験としては直感的でなく、特にWSL2やSSHリモートのような複数環境が混在する場合に混乱を招きやすい。設定ファイルの場所とサーバ起動場所の対応関係を明示するUIやドキュメントの改善が望まれる。

## 関連ノート
- [[VS Code Remote Development_技術アーキテクチャとAI統合]]
- [[Claude_Code_使える環境と動作概要]]
- [[MCPサーバ_Resource_Prompt_Tool.md]]
- [GitHub Issue: Allow running and detecting servers on WSL2 #243687](https://github.com/microsoft/vscode/issues/243687)

#vscode #mcp #wsl 
