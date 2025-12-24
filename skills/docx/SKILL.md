---
name: docx
description: "変更履歴、コメント、書式保持、テキスト抽出をサポートした包括的なドキュメントの作成、編集、分析機能。Claudeがプロフェッショナルなドキュメント（.docxファイル）を扱う必要がある場合：(1) 新規ドキュメントの作成、(2) コンテンツの変更または編集、(3) 変更履歴の操作、(4) コメントの追加、その他のドキュメントタスク"
license: Proprietary. LICENSE.txt has complete terms
---

# DOCXの作成、編集、分析

## 概要

ユーザーは.docxファイルの作成、編集、または内容の分析を依頼する場合があります。.docxファイルは本質的にXMLファイルとその他のリソースを含むZIPアーカイブであり、読み取りや編集が可能です。タスクに応じて異なるツールとワークフローが利用可能です。

## ワークフロー決定ツリー

### コンテンツの読み取り/分析
下記の「テキスト抽出」または「Raw XMLアクセス」セクションを使用

### 新規ドキュメントの作成
「新規Wordドキュメントの作成」ワークフローを使用

### 既存ドキュメントの編集
- **自分のドキュメント + 簡単な変更**
  「基本的なOOXML編集」ワークフローを使用

- **他者のドキュメント**
  **「レッドラインワークフロー」**を使用（推奨デフォルト）

- **法務、学術、ビジネス、または政府文書**
  **「レッドラインワークフロー」**を使用（必須）

## コンテンツの読み取りと分析

### テキスト抽出
ドキュメントのテキスト内容を読み取るだけの場合は、pandocを使用してドキュメントをmarkdownに変換します。Pandocはドキュメント構造の保持に優れており、変更履歴を表示できます：

```bash
# 変更履歴付きでドキュメントをmarkdownに変換
pandoc --track-changes=all path-to-file.docx -o output.md
# オプション: --track-changes=accept/reject/all
```

### Raw XMLアクセス
コメント、複雑な書式設定、ドキュメント構造、埋め込みメディア、メタデータにはRaw XMLアクセスが必要です。これらの機能には、ドキュメントを展開してRaw XMLコンテンツを読み取る必要があります。

#### ファイルの展開
`python ooxml/scripts/unpack.py <office_file> <output_directory>`

#### 主要なファイル構造
* `word/document.xml` - メインドキュメントコンテンツ
* `word/comments.xml` - document.xmlで参照されるコメント
* `word/media/` - 埋め込まれた画像とメディアファイル
* 変更履歴は`<w:ins>`（挿入）と`<w:del>`（削除）タグを使用

## 新規Wordドキュメントの作成

新規Wordドキュメントをゼロから作成する場合は、JavaScript/TypeScriptでWordドキュメントを作成できる**docx-js**を使用します。

### ワークフロー
1. **必須 - ファイル全体を読む**: [`docx-js.md`](docx-js.md)（約500行）を最初から最後まで完全に読んでください。**このファイルを読む際に範囲制限を設定しないでください。** ドキュメント作成を進める前に、詳細な構文、重要な書式設定ルール、ベストプラクティスのためにファイル全体を読んでください。
2. Document、Paragraph、TextRunコンポーネントを使用してJavaScript/TypeScriptファイルを作成（すべての依存関係がインストールされていると仮定しますが、インストールされていない場合は下記の依存関係セクションを参照）
3. Packer.toBuffer()を使用して.docxとしてエクスポート

## 既存Wordドキュメントの編集

既存のWordドキュメントを編集する場合は、**Documentライブラリ**（OOXML操作用のPythonライブラリ）を使用します。ライブラリはインフラストラクチャのセットアップを自動的に処理し、ドキュメント操作のためのメソッドを提供します。複雑なシナリオでは、ライブラリを通じて基盤となるDOMに直接アクセスできます。

### ワークフロー
1. **必須 - ファイル全体を読む**: [`ooxml.md`](ooxml.md)（約600行）を最初から最後まで完全に読んでください。**このファイルを読む際に範囲制限を設定しないでください。** ドキュメントファイルを直接編集するためのDocumentライブラリAPIとXMLパターンについてファイル全体を読んでください。
2. ドキュメントを展開: `python ooxml/scripts/unpack.py <office_file> <output_directory>`
3. Documentライブラリを使用してPythonスクリプトを作成・実行（ooxml.mdの「Documentライブラリ」セクションを参照）
4. 最終ドキュメントをパック: `python ooxml/scripts/pack.py <input_directory> <office_file>`

Documentライブラリは一般的な操作のための高レベルメソッドと、複雑なシナリオのための直接DOMアクセスの両方を提供します。

## ドキュメントレビュー用のレッドラインワークフロー

このワークフローでは、OOXMLで実装する前にmarkdownを使用して包括的な変更履歴を計画できます。**重要**: 完全な変更履歴を作成するには、すべての変更を体系的に実装する必要があります。

**バッチ戦略**: 関連する変更を3-10の変更のバッチにグループ化します。これによりデバッグが管理しやすくなりながら効率を維持できます。次のバッチに進む前に各バッチをテストしてください。

**原則: 最小限で正確な編集**
変更履歴を実装する際は、実際に変更されたテキストのみをマークします。変更されていないテキストを繰り返すと編集のレビューが難しくなり、プロフェッショナルでない印象を与えます。置換を次のように分割: [変更なしテキスト] + [削除] + [挿入] + [変更なしテキスト]。元の`<w:r>`要素を抽出して再利用することで、変更なしテキストの元のランのRSIDを保持します。

例 - 文中の「30 days」を「60 days」に変更:
```python
# 悪い例 - 文全体を置換
'<w:del><w:r><w:delText>The term is 30 days.</w:delText></w:r></w:del><w:ins><w:r><w:t>The term is 60 days.</w:t></w:r></w:ins>'

# 良い例 - 変更部分のみをマークし、変更なしテキストの元の<w:r>を保持
'<w:r w:rsidR="00AB12CD"><w:t>The term is </w:t></w:r><w:del><w:r><w:delText>30</w:delText></w:r></w:del><w:ins><w:r><w:t>60</w:t></w:r></w:ins><w:r w:rsidR="00AB12CD"><w:t> days.</w:t></w:r>'
```

### 変更履歴ワークフロー

1. **markdown表現を取得**: 変更履歴を保持してドキュメントをmarkdownに変換:
   ```bash
   pandoc --track-changes=all path-to-file.docx -o current.md
   ```

2. **変更を特定してグループ化**: ドキュメントをレビューして必要なすべての変更を特定し、論理的なバッチに整理:

   **場所特定方法**（XMLで変更を見つけるため）:
   - セクション/見出し番号（例: 「Section 3.2」、「Article IV」）
   - 番号付きの場合は段落識別子
   - ユニークな周囲テキストを使用したgrepパターン
   - ドキュメント構造（例: 「first paragraph」、「signature block」）
   - **markdownの行番号は使用しない** - XML構造にマッピングされない

   **バッチ整理**（バッチあたり3-10の関連する変更をグループ化）:
   - セクション別: 「Batch 1: Section 2 amendments」、「Batch 2: Section 5 updates」
   - タイプ別: 「Batch 1: Date corrections」、「Batch 2: Party name changes」
   - 複雑さ別: 単純なテキスト置換から始め、複雑な構造変更に取り組む
   - 順序別: 「Batch 1: Pages 1-3」、「Batch 2: Pages 4-6」

3. **ドキュメントを読んで展開**:
   - **必須 - ファイル全体を読む**: [`ooxml.md`](ooxml.md)（約600行）を最初から最後まで完全に読んでください。**このファイルを読む際に範囲制限を設定しないでください。** 特に「Documentライブラリ」と「変更履歴パターン」セクションに注意してください。
   - **ドキュメントを展開**: `python ooxml/scripts/unpack.py <file.docx> <dir>`
   - **推奨RSIDに注意**: unpackスクリプトは変更履歴に使用するRSIDを提案します。ステップ4bで使用するためにこのRSIDをコピーしてください。

4. **バッチで変更を実装**: 変更を論理的にグループ化（セクション別、タイプ別、または近接性別）し、単一のスクリプトでまとめて実装します。このアプローチ:
   - デバッグを容易にする（小さいバッチ = エラーの分離が容易）
   - 段階的な進捗を可能にする
   - 効率を維持（3-10変更のバッチサイズが適切）

   **推奨バッチグループ化:**
   - ドキュメントセクション別（例: 「Section 3 changes」、「Definitions」、「Termination clause」）
   - 変更タイプ別（例: 「Date changes」、「Party name updates」、「Legal term replacements」）
   - 近接性別（例: 「Changes on pages 1-3」、「Changes in first half of document」）

   関連する変更の各バッチについて:

   **a. テキストをXMLにマッピング**: `word/document.xml`でテキストをgrepして、テキストが`<w:r>`要素間でどのように分割されているかを確認。

   **b. スクリプトを作成して実行**: `get_node`を使用してノードを見つけ、変更を実装し、`doc.save()`を実行。パターンについてはooxml.mdの**「Documentライブラリ」**セクションを参照。

   **注意**: スクリプトを書く直前に常に`word/document.xml`をgrepして、現在の行番号を取得しテキスト内容を確認してください。行番号は各スクリプト実行後に変わります。

5. **ドキュメントをパック**: すべてのバッチが完了したら、展開されたディレクトリを.docxに戻す:
   ```bash
   python ooxml/scripts/pack.py unpacked reviewed-document.docx
   ```

6. **最終検証**: 完全なドキュメントの包括的なチェックを行う:
   - 最終ドキュメントをmarkdownに変換:
     ```bash
     pandoc --track-changes=all reviewed-document.docx -o verification.md
     ```
   - すべての変更が正しく適用されたことを確認:
     ```bash
     grep "original phrase" verification.md  # 見つからないはず
     grep "replacement phrase" verification.md  # 見つかるはず
     ```
   - 意図しない変更が導入されていないことを確認


## ドキュメントを画像に変換

Wordドキュメントを視覚的に分析するには、2段階のプロセスで画像に変換します:

1. **DOCXをPDFに変換**:
   ```bash
   soffice --headless --convert-to pdf document.docx
   ```

2. **PDFページをJPEG画像に変換**:
   ```bash
   pdftoppm -jpeg -r 150 document.pdf page
   ```
   これにより`page-1.jpg`、`page-2.jpg`などのファイルが作成されます。

オプション:
- `-r 150`: 解像度を150 DPIに設定（品質/サイズのバランスを調整）
- `-jpeg`: JPEG形式で出力（PNGを好む場合は`-png`を使用）
- `-f N`: 変換開始ページ（例: `-f 2`でページ2から開始）
- `-l N`: 変換終了ページ（例: `-l 5`でページ5で停止）
- `page`: 出力ファイルのプレフィックス

特定範囲の例:
```bash
pdftoppm -jpeg -r 150 -f 2 -l 5 document.pdf page  # ページ2-5のみ変換
```

## コードスタイルガイドライン
**重要**: DOCX操作用のコードを生成する際:
- 簡潔なコードを書く
- 冗長な変数名や重複した操作を避ける
- 不要なprint文を避ける

## 依存関係

必要な依存関係（利用できない場合はインストール）:

- **pandoc**: `sudo apt-get install pandoc`（テキスト抽出用）
- **docx**: `npm install -g docx`（新規ドキュメント作成用）
- **LibreOffice**: `sudo apt-get install libreoffice`（PDF変換用）
- **Poppler**: `sudo apt-get install poppler-utils`（pdftoppmでPDFを画像に変換）
- **defusedxml**: `pip install defusedxml`（安全なXML解析用）
