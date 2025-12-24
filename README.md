> **注記:** このリポジトリは[anthropics/skills](https://github.com/anthropics/skills)の日本語版フォークです。Agent Skillsの仕様については[agentskills.io](http://agentskills.io)を参照してください。

# スキル
スキルは、Claudeが専門的なタスクのパフォーマンスを向上させるために動的に読み込む、指示、スクリプト、リソースのフォルダです。スキルはClaudeに特定のタスクを再現可能な方法で完了させる方法を教えます。例えば、会社のブランドガイドラインに沿ったドキュメントの作成、組織固有のワークフローを使用したデータ分析、個人的なタスクの自動化などです。

詳細については以下を参照してください：
- [スキルとは？](https://support.claude.com/en/articles/12512176-what-are-skills)
- [Claudeでスキルを使用する](https://support.claude.com/en/articles/12512180-using-skills-in-claude)
- [カスタムスキルの作成方法](https://support.claude.com/en/articles/12512198-creating-custom-skills)
- [Agent Skillsでエージェントを実世界に対応させる](https://anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills)

# このリポジトリについて

このリポジトリには、Claudeのスキルシステムで何ができるかを示すスキルが含まれています。これらのスキルは、クリエイティブなアプリケーション（アート、音楽、デザイン）から技術的なタスク（Webアプリのテスト、MCPサーバー生成）、エンタープライズワークフロー（コミュニケーション、ブランディングなど）まで多岐にわたります。

各スキルは独自のフォルダに自己完結しており、Claudeが使用する指示とメタデータを含む`SKILL.md`ファイルがあります。これらのスキルを参照して、自分のスキルのインスピレーションを得たり、さまざまなパターンやアプローチを理解したりしてください。

このリポジトリの多くのスキルはオープンソース（Apache 2.0）です。また、[Claudeのドキュメント機能](https://www.anthropic.com/news/create-files)を裏で支えるドキュメント作成・編集スキルも、[`skills/docx`](./skills/docx)、[`skills/pdf`](./skills/pdf)、[`skills/pptx`](./skills/pptx)、[`skills/xlsx`](./skills/xlsx)サブフォルダに含めています。これらはオープンソースではなくソース公開ですが、本番AIアプリケーションで実際に使用されているより複雑なスキルの参考として開発者と共有したいと考えました。

## 免責事項

**これらのスキルはデモンストレーションおよび教育目的のみで提供されています。** これらの機能の一部はClaudeで利用可能かもしれませんが、Claudeから受け取る実装と動作はこれらのスキルに示されているものと異なる場合があります。これらのスキルはパターンと可能性を示すことを目的としています。重要なタスクに依存する前に、必ず自分の環境でスキルを十分にテストしてください。

# スキルセット
- [./skills](./skills): クリエイティブ＆デザイン、開発＆技術、エンタープライズ＆コミュニケーション、ドキュメントスキルの例
- [./spec](./spec): Agent Skills仕様
- [./template](./template): スキルテンプレート

# Claude Code、Claude.ai、APIで試す

## Claude Code
Claude Codeで以下のコマンドを実行して、このリポジトリをClaude Codeプラグインマーケットプレイスとして登録できます：
```
/plugin marketplace add yamato-snow/skills
```

次に、特定のスキルセットをインストールするには：
1. `Browse and install plugins`を選択
2. `anthropic-agent-skills-ja`を選択
3. `document-skills`または`example-skills`を選択
4. `Install now`を選択

または、以下のコマンドで直接プラグインをインストール：
```
/plugin install document-skills@anthropic-agent-skills-ja
/plugin install example-skills@anthropic-agent-skills-ja
```

プラグインをインストールした後、そのスキルに言及するだけで使用できます。例えば、マーケットプレイスから`document-skills`プラグインをインストールした場合、Claude Codeに次のようなリクエストができます：「PDFスキルを使用して`path/to/some-file.pdf`からフォームフィールドを抽出してください」

## Claude.ai

これらのサンプルスキルはすべて、Claude.aiの有料プランですでに利用可能です。

このリポジトリのスキルを使用したり、カスタムスキルをアップロードしたりするには、[Claudeでスキルを使用する](https://support.claude.com/en/articles/12512180-using-skills-in-claude#h_a4222fa77b)の手順に従ってください。

## Claude API

Claude APIを通じて、Anthropicの事前構築スキルを使用したり、カスタムスキルをアップロードしたりできます。詳細は[Skills APIクイックスタート](https://docs.claude.com/en/api/skills-guide#creating-a-skill)を参照してください。

# 基本的なスキルの作成

スキルの作成は簡単です。YAMLフロントマターと指示を含む`SKILL.md`ファイルがあるフォルダだけで済みます。このリポジトリの**template-skill**を出発点として使用できます：

```markdown
---
name: my-skill-name
description: このスキルが何をするか、いつ使用するかの明確な説明
---

# マイスキル名

[このスキルがアクティブなときにClaudeが従う指示をここに追加]

## 例
- 使用例1
- 使用例2

## ガイドライン
- ガイドライン1
- ガイドライン2
```

フロントマターには2つのフィールドのみが必要です：
- `name` - スキルの一意の識別子（小文字、スペースにはハイフン）
- `description` - スキルが何をするか、いつ使用するかの完全な説明

下のMarkdownコンテンツには、Claudeが従う指示、例、ガイドラインが含まれています。詳細については、[カスタムスキルの作成方法](https://support.claude.com/en/articles/12512198-creating-custom-skills)を参照してください。

# パートナースキル

スキルは、Claudeに特定のソフトウェアの使い方を教えるのに最適な方法です。パートナーからの優れたサンプルスキルを見つけたら、ここで紹介することがあります：

- **Notion** - [Notion Skills for Claude](https://www.notion.so/notiondevs/Notion-Skills-for-Claude-28da4445d27180c7af1df7d8615723d0)
