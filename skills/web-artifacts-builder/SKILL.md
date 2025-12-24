---
name: web-artifacts-builder
description: モダンなフロントエンドWeb技術（React、Tailwind CSS、shadcn/ui）を使用して、精巧なマルチコンポーネントのclaude.ai HTMLアーティファクトを作成するためのツールスイート。状態管理、ルーティング、またはshadcn/uiコンポーネントを必要とする複雑なアーティファクトに使用 - シンプルな単一ファイルHTML/JSXアーティファクトには不向き。
license: Complete terms in LICENSE.txt
---

# Web Artifacts Builder

強力なフロントエンドclaude.aiアーティファクトを構築するには、以下の手順に従う：
1. `scripts/init-artifact.sh`を使用してフロントエンドリポジトリを初期化
2. 生成されたコードを編集してアーティファクトを開発
3. `scripts/bundle-artifact.sh`を使用してすべてのコードを単一のHTMLファイルにバンドル
4. ユーザーにアーティファクトを表示
5. （オプション）アーティファクトをテスト

**スタック**：React 18 + TypeScript + Vite + Parcel（バンドリング）+ Tailwind CSS + shadcn/ui

## デザイン＆スタイルガイドライン

非常に重要：「AIスロップ」と呼ばれるものを避けるため、過度な中央揃えレイアウト、紫のグラデーション、均一な角丸、Interフォントの使用を避ける。

## クイックスタート

### ステップ1：プロジェクトの初期化

初期化スクリプトを実行して新しいReactプロジェクトを作成：
```bash
bash scripts/init-artifact.sh <project-name>
cd <project-name>
```

これにより、以下が完全に設定されたプロジェクトが作成される：
- ✅ React + TypeScript（Vite経由）
- ✅ shadcn/uiテーマシステム付きTailwind CSS 3.4.1
- ✅ パスエイリアス（`@/`）設定済み
- ✅ 40以上のshadcn/uiコンポーネントがプリインストール
- ✅ すべてのRadix UI依存関係を含む
- ✅ バンドリング用にParcel設定済み（.parcelrc経由）
- ✅ Node 18+互換性（Viteバージョンを自動検出してピン留め）

### ステップ2：アーティファクトの開発

アーティファクトを構築するには、生成されたファイルを編集。ガイダンスについては下記の**一般的な開発タスク**を参照。

### ステップ3：単一HTMLファイルへのバンドル

Reactアプリを単一のHTMLアーティファクトにバンドルするには：
```bash
bash scripts/bundle-artifact.sh
```

これにより`bundle.html`が作成される - すべてのJavaScript、CSS、依存関係がインライン化された自己完結型アーティファクト。このファイルはClaudeの会話でアーティファクトとして直接共有可能。

**要件**：プロジェクトのルートディレクトリに`index.html`が必要。

**スクリプトの動作**：
- バンドリング依存関係をインストール（parcel、@parcel/config-default、parcel-resolver-tspaths、html-inline）
- パスエイリアスサポート付きの`.parcelrc`設定を作成
- Parcelでビルド（ソースマップなし）
- html-inlineを使用してすべてのアセットを単一HTMLにインライン化

### ステップ4：ユーザーとアーティファクトを共有

最後に、バンドルされたHTMLファイルを会話でユーザーと共有し、アーティファクトとして閲覧できるようにする。

### ステップ5：アーティファクトのテスト/視覚化（オプション）

注意：これは完全にオプションのステップ。必要な場合またはリクエストされた場合のみ実行。

アーティファクトをテスト/視覚化するには、利用可能なツール（他のスキルやPlaywrightまたはPuppeteerなどの組み込みツールを含む）を使用。一般的に、リクエストから完成したアーティファクトが表示されるまでのレイテンシーを増やすため、事前にアーティファクトをテストすることは避ける。リクエストされた場合または問題が発生した場合に、アーティファクトを提示した後にテスト。

## リファレンス

- **shadcn/uiコンポーネント**：https://ui.shadcn.com/docs/components
