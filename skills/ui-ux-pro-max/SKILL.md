---
name: ui-ux-pro-max
description: "UI/UXデザインインテリジェンス。57スタイル、95カラーパレット、56フォントペアリング、24チャートタイプ、8スタック（React、Next.js、Vue、Svelte、SwiftUI、React Native、Flutter、Tailwind）対応。アクション：plan、build、create、design、implement、review、fix、improve、optimize、enhance、refactor、UI/UXコードのチェック。プロジェクト：ウェブサイト、ランディングページ、ダッシュボード、管理パネル、EC、SaaS、ポートフォリオ、ブログ、モバイルアプリ、.html、.tsx、.vue、.svelte。要素：ボタン、モーダル、ナビバー、サイドバー、カード、テーブル、フォーム、チャート。スタイル：グラスモーフィズム、クレイモーフィズム、ミニマリズム、ブルータリズム、ニューモーフィズム、ベントグリッド、ダークモード、レスポンシブ、スキューモーフィズム、フラットデザイン。トピック：カラーパレット、アクセシビリティ、アニメーション、レイアウト、タイポグラフィ、フォントペアリング、スペーシング、ホバー、シャドウ、グラデーション。"
license: MIT
---

# UI/UX Pro Max - デザインインテリジェンス

UIスタイル、カラーパレット、フォントペアリング、チャートタイプ、プロダクト推奨、UXガイドライン、スタック別ベストプラクティスの検索可能なデータベース。

## 前提条件

Pythonがインストールされているか確認：

```bash
python3 --version || python --version
```

Pythonがインストールされていない場合、OSに応じてインストール：

**macOS:**
```bash
brew install python3
```

**Ubuntu/Debian:**
```bash
sudo apt update && sudo apt install python3
```

**Windows:**
```powershell
winget install Python.Python.3.12
```

---

## このスキルの使い方

ユーザーがUI/UX作業（デザイン、構築、作成、実装、レビュー、修正、改善）をリクエストした場合、以下のワークフローに従ってください：

### ステップ1：ユーザー要件を分析

ユーザーリクエストから重要な情報を抽出：
- **プロダクトタイプ**: SaaS、EC、ポートフォリオ、ダッシュボード、ランディングページなど
- **スタイルキーワード**: ミニマル、プレイフル、プロフェッショナル、エレガント、ダークモードなど
- **業界**: ヘルスケア、フィンテック、ゲーム、教育など
- **スタック**: React、Vue、Next.js、またはデフォルトで `html-tailwind`

### ステップ2：関連ドメインを検索

`search.py` を複数回使用して包括的な情報を収集。十分なコンテキストが得られるまで検索を続けてください。

```bash
python3 skills/ui-ux-pro-max/scripts/search.py "<keyword>" --domain <domain> [-n <max_results>]
```

**推奨される検索順序：**

1. **Product** - プロダクトタイプに対するスタイル推奨を取得
2. **Style** - 詳細なスタイルガイド（色、エフェクト、フレームワーク）を取得
3. **Typography** - Google Fontsインポート付きのフォントペアリングを取得
4. **Color** - カラーパレット（Primary、Secondary、CTA、Background、Text、Border）を取得
5. **Landing** - ページ構造を取得（ランディングページの場合）
6. **Chart** - チャート推奨を取得（ダッシュボード/アナリティクスの場合）
7. **UX** - ベストプラクティスとアンチパターンを取得
8. **Stack** - スタック固有のガイドラインを取得（デフォルト: html-tailwind）

### ステップ3：スタックガイドライン（デフォルト: html-tailwind）

ユーザーがスタックを指定しない場合、**デフォルトで `html-tailwind` を使用**。

```bash
python3 skills/ui-ux-pro-max/scripts/search.py "<keyword>" --stack html-tailwind
```

利用可能なスタック: `html-tailwind`、`react`、`nextjs`、`vue`、`svelte`、`swiftui`、`react-native`、`flutter`

---

## 検索リファレンス

### 利用可能なドメイン

| ドメイン | 用途 | キーワード例 |
|---------|------|-------------|
| `product` | プロダクトタイプの推奨 | SaaS、e-commerce、portfolio、healthcare、beauty、service |
| `style` | UIスタイル、色、エフェクト | glassmorphism、minimalism、dark mode、brutalism |
| `typography` | フォントペアリング、Google Fonts | elegant、playful、professional、modern |
| `color` | プロダクトタイプ別カラーパレット | saas、ecommerce、healthcare、beauty、fintech、service |
| `landing` | ページ構造、CTA戦略 | hero、hero-centric、testimonial、pricing、social-proof |
| `chart` | チャートタイプ、ライブラリ推奨 | trend、comparison、timeline、funnel、pie |
| `ux` | ベストプラクティス、アンチパターン | animation、accessibility、z-index、loading |
| `prompt` | AIプロンプト、CSSキーワード | （スタイル名） |

### 利用可能なスタック

| スタック | フォーカス |
|---------|----------|
| `html-tailwind` | Tailwindユーティリティ、レスポンシブ、a11y（デフォルト） |
| `react` | ステート、フック、パフォーマンス、パターン |
| `nextjs` | SSR、ルーティング、画像、APIルート |
| `vue` | Composition API、Pinia、Vue Router |
| `svelte` | Runes、ストア、SvelteKit |
| `swiftui` | ビュー、ステート、ナビゲーション、アニメーション |
| `react-native` | コンポーネント、ナビゲーション、リスト |
| `flutter` | ウィジェット、ステート、レイアウト、テーマ |

---

## ワークフロー例

**ユーザーリクエスト:** 「プロフェッショナルなスキンケアサービスのランディングページを作成」

**AIが実行すべき操作：**

```bash
# 1. プロダクトタイプを検索
python3 skills/ui-ux-pro-max/scripts/search.py "beauty spa wellness service" --domain product

# 2. スタイルを検索（業界: beauty、elegant）
python3 skills/ui-ux-pro-max/scripts/search.py "elegant minimal soft" --domain style

# 3. タイポグラフィを検索
python3 skills/ui-ux-pro-max/scripts/search.py "elegant luxury" --domain typography

# 4. カラーパレットを検索
python3 skills/ui-ux-pro-max/scripts/search.py "beauty spa wellness" --domain color

# 5. ランディングページ構造を検索
python3 skills/ui-ux-pro-max/scripts/search.py "hero-centric social-proof" --domain landing

# 6. UXガイドラインを検索
python3 skills/ui-ux-pro-max/scripts/search.py "animation" --domain ux
python3 skills/ui-ux-pro-max/scripts/search.py "accessibility" --domain ux

# 7. スタックガイドラインを検索（デフォルト: html-tailwind）
python3 skills/ui-ux-pro-max/scripts/search.py "layout responsive" --stack html-tailwind
```

**その後:** すべての検索結果を統合してデザインを実装。

---

## より良い結果のためのヒント

1. **キーワードは具体的に** - 「app」より「healthcare SaaS dashboard」
2. **複数回検索** - 異なるキーワードで異なる洞察が得られる
3. **ドメインを組み合わせる** - Style + Typography + Color = 完全なデザインシステム
4. **常にUXをチェック** - 「animation」「z-index」「accessibility」で一般的な問題を検索
5. **stackフラグを使用** - 実装固有のベストプラクティスを取得
6. **反復する** - 最初の検索が合わない場合、異なるキーワードを試す

---

## プロフェッショナルUIのための共通ルール

UIがプロフェッショナルに見えない原因となる、見落とされがちな問題：

### アイコンとビジュアル要素

| ルール | 良い例 | 悪い例 |
|--------|-------|--------|
| **絵文字アイコン禁止** | SVGアイコンを使用（Heroicons、Lucide、Simple Icons） | UIアイコンとして絵文字を使用 |
| **安定したホバー状態** | ホバー時に色/透明度のトランジションを使用 | レイアウトをずらすスケール変換を使用 |
| **正確なブランドロゴ** | Simple Iconsから公式SVGを調査 | ロゴパスを推測したり不正確なものを使用 |
| **一貫したアイコンサイズ** | 固定viewBox（24x24）とw-6 h-6を使用 | 異なるアイコンサイズをランダムに混在 |

### インタラクションとカーソル

| ルール | 良い例 | 悪い例 |
|--------|-------|--------|
| **カーソルポインター** | クリック/ホバー可能なすべてのカードに`cursor-pointer`を追加 | インタラクティブ要素にデフォルトカーソルを残す |
| **ホバーフィードバック** | 視覚的フィードバックを提供（色、シャドウ、ボーダー） | 要素がインタラクティブであることを示さない |
| **スムーズなトランジション** | `transition-colors duration-200`を使用 | 即座の状態変更または遅すぎる（>500ms） |

### ライト/ダークモードのコントラスト

| ルール | 良い例 | 悪い例 |
|--------|-------|--------|
| **ライトモードのガラスカード** | `bg-white/80`または高い透明度を使用 | `bg-white/10`を使用（透明すぎる） |
| **ライトモードのテキストコントラスト** | テキストに`#0F172A`（slate-900）を使用 | 本文に`#94A3B8`（slate-400）を使用 |
| **ライトモードのミュートテキスト** | 最低`#475569`（slate-600）を使用 | gray-400以下を使用 |
| **ボーダーの可視性** | ライトモードで`border-gray-200`を使用 | `border-white/10`を使用（見えない） |

### レイアウトとスペーシング

| ルール | 良い例 | 悪い例 |
|--------|-------|--------|
| **フローティングナビバー** | `top-4 left-4 right-4`のスペーシングを追加 | ナビバーを`top-0 left-0 right-0`に固定 |
| **コンテンツパディング** | 固定ナビバーの高さを考慮 | コンテンツが固定要素の後ろに隠れる |
| **一貫したmax-width** | 同じ`max-w-6xl`または`max-w-7xl`を使用 | 異なるコンテナ幅を混在 |

---

## 納品前チェックリスト

UIコードを納品する前に、以下の項目を確認：

### ビジュアル品質
- [ ] アイコンとして絵文字を使用していない（代わりにSVGを使用）
- [ ] すべてのアイコンが一貫したアイコンセットから（Heroicons/Lucide）
- [ ] ブランドロゴが正確（Simple Iconsから確認）
- [ ] ホバー状態がレイアウトシフトを起こさない
- [ ] var()ラッパーではなくテーマカラーを直接使用（bg-primary）

### インタラクション
- [ ] すべてのクリック可能な要素に`cursor-pointer`がある
- [ ] ホバー状態が明確な視覚的フィードバックを提供
- [ ] トランジションがスムーズ（150-300ms）
- [ ] キーボードナビゲーション用のフォーカス状態が可視

### ライト/ダークモード
- [ ] ライトモードのテキストに十分なコントラスト（最低4.5:1）
- [ ] ガラス/透明要素がライトモードで可視
- [ ] 両モードでボーダーが可視
- [ ] 納品前に両モードをテスト

### レイアウト
- [ ] フローティング要素が端から適切なスペーシングを持つ
- [ ] 固定ナビバーの後ろにコンテンツが隠れない
- [ ] 320px、768px、1024px、1440pxでレスポンシブ
- [ ] モバイルで水平スクロールなし

### アクセシビリティ
- [ ] すべての画像にalt属性がある
- [ ] フォーム入力にラベルがある
- [ ] 色だけが唯一の指標ではない
- [ ] `prefers-reduced-motion`を尊重

---

## ライセンスと帰属

このスキルは [nextlevelbuilder/ui-ux-pro-max-skill](https://github.com/nextlevelbuilder/ui-ux-pro-max-skill) をベースに日本語化したものです。

**Original Author:** nextlevelbuilder
**License:** MIT License
**Japanese Translation:** skills-ja project

詳細は同梱の `LICENSE` ファイルを参照してください。
