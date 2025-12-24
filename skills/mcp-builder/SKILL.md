---
name: mcp-builder
description: LLMが適切に設計されたツールを通じて外部サービスと連携できる、高品質なMCP（Model Context Protocol）サーバーを作成するためのガイド。外部APIやサービスを統合するMCPサーバーを構築する場合に使用（Python（FastMCP）またはNode/TypeScript（MCP SDK）対応）。
license: Complete terms in LICENSE.txt
---

# MCPサーバー開発ガイド

## 概要

LLMが適切に設計されたツールを通じて外部サービスと連携できるMCP（Model Context Protocol）サーバーを作成。MCPサーバーの品質は、LLMが実世界のタスクをいかにうまく達成できるかで測定される。

---

# プロセス

## 🚀 ハイレベルワークフロー

高品質なMCPサーバーの作成は4つの主要フェーズを含む：

### フェーズ1：詳細な調査と計画

#### 1.1 最新のMCP設計を理解

**APIカバレッジ vs ワークフローツール：**
包括的なAPIエンドポイントカバレッジと特殊化されたワークフローツールのバランスを取る。ワークフローツールは特定のタスクでより便利な場合があり、包括的なカバレッジはエージェントに操作を組み合わせる柔軟性を与える。パフォーマンスはクライアントによって異なる—基本ツールを組み合わせるコード実行の恩恵を受けるクライアントもあれば、より高レベルのワークフローでうまく機能するクライアントもある。不確かな場合は、包括的なAPIカバレッジを優先。

**ツールの命名と発見可能性：**
明確で説明的なツール名は、エージェントが適切なツールを素早く見つけるのに役立つ。一貫したプレフィックス（例：`github_create_issue`、`github_list_repos`）とアクション指向の命名を使用。

**コンテキスト管理：**
エージェントは簡潔なツール説明と結果のフィルタリング/ページネーション機能の恩恵を受ける。焦点を絞った関連データを返すツールを設計。一部のクライアントはコード実行をサポートし、エージェントが効率的にデータをフィルタリングおよび処理するのに役立つ。

**実行可能なエラーメッセージ：**
エラーメッセージは、具体的な提案と次のステップでエージェントをソリューションに導くべき。

#### 1.2 MCPプロトコルドキュメントを学習

**MCP仕様をナビゲート：**

関連ページを見つけるためにサイトマップから開始：`https://modelcontextprotocol.io/sitemap.xml`

次に、マークダウン形式で特定のページを`.md`サフィックス付きでフェッチ（例：`https://modelcontextprotocol.io/specification/draft.md`）。

レビューすべき主要ページ：
- 仕様の概要とアーキテクチャ
- トランスポートメカニズム（streamable HTTP、stdio）
- ツール、リソース、プロンプトの定義

#### 1.3 フレームワークドキュメントを学習

**推奨スタック：**
- **言語**：TypeScript（高品質なSDKサポートと多くの実行環境での良好な互換性（例：MCPB）。加えて、AIモデルはTypeScriptコード生成が得意で、その広い使用、静的型付け、良好なリンティングツールの恩恵を受ける）
- **トランスポート**：リモートサーバーにはStreamable HTTPを使用し、ステートレスJSON（ステートフルセッションやストリーミングレスポンスよりもスケールと保守が簡単）。ローカルサーバーにはstdio。

**フレームワークドキュメントを読み込む：**

- **MCPベストプラクティス**：[📋 ベストプラクティスを見る](./reference/mcp_best_practices.md) - コアガイドライン

**TypeScript用（推奨）：**
- **TypeScript SDK**：WebFetchを使用して`https://raw.githubusercontent.com/modelcontextprotocol/typescript-sdk/main/README.md`を読み込む
- [⚡ TypeScriptガイド](./reference/node_mcp_server.md) - TypeScriptパターンと例

**Python用：**
- **Python SDK**：WebFetchを使用して`https://raw.githubusercontent.com/modelcontextprotocol/python-sdk/main/README.md`を読み込む
- [🐍 Pythonガイド](./reference/python_mcp_server.md) - Pythonパターンと例

#### 1.4 実装を計画

**APIを理解：**
サービスのAPIドキュメントをレビューし、主要なエンドポイント、認証要件、データモデルを特定。必要に応じてWeb検索とWebFetchを使用。

**ツール選択：**
包括的なAPIカバレッジを優先。実装するエンドポイントをリストし、最も一般的な操作から開始。

---

### フェーズ2：実装

#### 2.1 プロジェクト構造のセットアップ

プロジェクトセットアップについては言語固有のガイドを参照：
- [⚡ TypeScriptガイド](./reference/node_mcp_server.md) - プロジェクト構造、package.json、tsconfig.json
- [🐍 Pythonガイド](./reference/python_mcp_server.md) - モジュール構成、依存関係

#### 2.2 コアインフラストラクチャの実装

共有ユーティリティを作成：
- 認証付きAPIクライアント
- エラーハンドリングヘルパー
- レスポンスフォーマット（JSON/Markdown）
- ページネーションサポート

#### 2.3 ツールの実装

各ツールについて：

**入力スキーマ：**
- Zod（TypeScript）またはPydantic（Python）を使用
- 制約と明確な説明を含める
- フィールド説明に例を追加

**出力スキーマ：**
- 可能な場合は構造化データのために`outputSchema`を定義
- ツールレスポンスで`structuredContent`を使用（TypeScript SDK機能）
- クライアントがツール出力を理解し処理するのを支援

**ツール説明：**
- 機能の簡潔な要約
- パラメータ説明
- 戻り値の型スキーマ

**実装：**
- I/O操作にはasync/await
- 実行可能なメッセージ付きの適切なエラーハンドリング
- 該当する場合はページネーションをサポート
- モダンSDK使用時はテキストコンテンツと構造化データの両方を返す

**アノテーション：**
- `readOnlyHint`：true/false
- `destructiveHint`：true/false
- `idempotentHint`：true/false
- `openWorldHint`：true/false

---

### フェーズ3：レビューとテスト

#### 3.1 コード品質

以下をレビュー：
- 重複コードなし（DRY原則）
- 一貫したエラーハンドリング
- 完全な型カバレッジ
- 明確なツール説明

#### 3.2 ビルドとテスト

**TypeScript：**
- `npm run build`を実行してコンパイルを確認
- MCP Inspectorでテスト：`npx @modelcontextprotocol/inspector`

**Python：**
- 構文を確認：`python -m py_compile your_server.py`
- MCP Inspectorでテスト

詳細なテストアプローチと品質チェックリストについては言語固有のガイドを参照。

---

### フェーズ4：評価の作成

MCPサーバーの実装後、その効果をテストする包括的な評価を作成。

**完全な評価ガイドラインについては[✅ 評価ガイド](./reference/evaluation.md)を読み込む。**

#### 4.1 評価の目的を理解

評価を使用して、LLMがMCPサーバーを効果的に使用して現実的で複雑な質問に回答できるかをテスト。

#### 4.2 10個の評価質問を作成

効果的な評価を作成するには、評価ガイドに記載されているプロセスに従う：

1. **ツール検査**：利用可能なツールをリストし、その機能を理解
2. **コンテンツ探索**：READ-ONLY操作を使用して利用可能なデータを探索
3. **質問生成**：10個の複雑で現実的な質問を作成
4. **回答検証**：各質問を自分で解いて回答を確認

#### 4.3 評価要件

各質問が以下を満たすことを確認：
- **独立性**：他の質問に依存しない
- **読み取り専用**：非破壊的な操作のみ必要
- **複雑さ**：複数のツール呼び出しと深い探索が必要
- **現実性**：人間が関心を持つ実際のユースケースに基づく
- **検証可能性**：文字列比較で検証できる単一の明確な回答
- **安定性**：回答が時間とともに変化しない

#### 4.4 出力形式

この構造でXMLファイルを作成：

```xml
<evaluation>
  <qa_pair>
    <question>動物のコードネームを持つAIモデルの発売に関するディスカッションを見つけてください。あるモデルはASL-Xという形式を使用する特定の安全指定が必要でした。斑点のある野生の猫にちなんで名付けられたモデルには何番Xが決定されていましたか？</question>
    <answer>3</answer>
  </qa_pair>
<!-- その他のqa_pair... -->
</evaluation>
```

---

# リファレンスファイル

## 📚 ドキュメントライブラリ

開発中に必要に応じてこれらのリソースを読み込む：

### コアMCPドキュメント（最初に読み込む）
- **MCPプロトコル**：`https://modelcontextprotocol.io/sitemap.xml`のサイトマップから開始し、`.md`サフィックス付きで特定のページをフェッチ
- [📋 MCPベストプラクティス](./reference/mcp_best_practices.md) - 以下を含む普遍的なMCPガイドライン：
  - サーバーとツールの命名規則
  - レスポンス形式ガイドライン（JSON vs Markdown）
  - ページネーションのベストプラクティス
  - トランスポート選択（streamable HTTP vs stdio）
  - セキュリティとエラーハンドリング基準

### SDKドキュメント（フェーズ1/2で読み込む）
- **Python SDK**：`https://raw.githubusercontent.com/modelcontextprotocol/python-sdk/main/README.md`からフェッチ
- **TypeScript SDK**：`https://raw.githubusercontent.com/modelcontextprotocol/typescript-sdk/main/README.md`からフェッチ

### 言語固有の実装ガイド（フェーズ2で読み込む）
- [🐍 Python実装ガイド](./reference/python_mcp_server.md) - 完全なPython/FastMCPガイド：
  - サーバー初期化パターン
  - Pydanticモデル例
  - `@mcp.tool`によるツール登録
  - 完全な動作例
  - 品質チェックリスト

- [⚡ TypeScript実装ガイド](./reference/node_mcp_server.md) - 完全なTypeScriptガイド：
  - プロジェクト構造
  - Zodスキーマパターン
  - `server.registerTool`によるツール登録
  - 完全な動作例
  - 品質チェックリスト

### 評価ガイド（フェーズ4で読み込む）
- [✅ 評価ガイド](./reference/evaluation.md) - 完全な評価作成ガイド：
  - 質問作成ガイドライン
  - 回答検証戦略
  - XML形式仕様
  - 質問と回答の例
  - 提供されたスクリプトによる評価の実行
