---
name: xlsx
description: "数式、書式設定、データ分析、可視化をサポートした包括的なスプレッドシートの作成、編集、分析機能。Claudeがスプレッドシート（.xlsx, .xlsm, .csv, .tsv等）を扱う必要がある場合：(1) 数式と書式設定を含む新規スプレッドシートの作成、(2) データの読み取りまたは分析、(3) 数式を保持しながら既存スプレッドシートを変更、(4) スプレッドシートでのデータ分析と可視化、(5) 数式の再計算"
license: Proprietary. LICENSE.txt has complete terms
---

# 出力の要件

## すべてのExcelファイル

### 数式エラーゼロ
- すべてのExcelモデルは、数式エラー（#REF!, #DIV/0!, #VALUE!, #N/A, #NAME?）がゼロの状態で納品する必要があります

### 既存テンプレートの保持（テンプレート更新時）
- ファイル変更時は既存のフォーマット、スタイル、規則を確認し正確に一致させる
- 確立されたパターンを持つファイルに標準化されたフォーマットを強制しない
- 既存のテンプレート規則は常にこれらのガイドラインより優先される

## 財務モデル

### 色分け規準
ユーザーまたは既存テンプレートで別途指定されていない限り

#### 業界標準の色規則
- **青色テキスト (RGB: 0,0,255)**: ハードコードされた入力値、シナリオ用にユーザーが変更する数値
- **黒色テキスト (RGB: 0,0,0)**: すべての数式と計算
- **緑色テキスト (RGB: 0,128,0)**: 同一ブック内の他のワークシートからプルするリンク
- **赤色テキスト (RGB: 255,0,0)**: 他のファイルへの外部リンク
- **黄色背景 (RGB: 255,255,0)**: 注意が必要な主要前提条件または更新が必要なセル

### 数値フォーマット規準

#### 必須フォーマットルール
- **年**: テキスト文字列としてフォーマット（例: "2024"、"2,024"ではない）
- **通貨**: $#,##0形式を使用; ヘッダーで必ず単位を指定（"Revenue ($mm)"）
- **ゼロ**: 数値フォーマットですべてのゼロを"-"にする、パーセンテージを含む（例: "$#,##0;($#,##0);-"）
- **パーセンテージ**: デフォルトで0.0%形式（小数点以下1桁）
- **倍率**: バリュエーション倍率（EV/EBITDA, P/E）は0.0x形式
- **負の数値**: マイナス記号-123ではなく括弧(123)を使用

### 数式構築ルール

#### 前提条件の配置
- すべての前提条件（成長率、マージン、倍率など）を別の前提条件セルに配置
- 数式内でハードコードされた値の代わりにセル参照を使用
- 例: =B5*1.05の代わりに=B5*(1+$B$6)を使用

#### 数式エラーの防止
- すべてのセル参照が正しいことを確認
- 範囲のオフバイワンエラーをチェック
- すべての予測期間で一貫した数式を確保
- エッジケース（ゼロ値、負の数値）でテスト
- 意図しない循環参照がないことを確認

#### ハードコードのドキュメント要件
- セルにコメントするか、横に記載（テーブルの末尾の場合）。形式: "Source: [システム/ドキュメント], [日付], [具体的な参照], [該当する場合はURL]"
- 例:
  - "Source: Company 10-K, FY2024, Page 45, Revenue Note, [SEC EDGAR URL]"
  - "Source: Company 10-Q, Q2 2025, Exhibit 99.1, [SEC EDGAR URL]"
  - "Source: Bloomberg Terminal, 8/15/2025, AAPL US Equity"
  - "Source: FactSet, 8/20/2025, Consensus Estimates Screen"

# XLSXの作成、編集、分析

## 概要

ユーザーは.xlsxファイルの作成、編集、または内容の分析を依頼する場合があります。タスクに応じて異なるツールとワークフローが利用可能です。

## 重要な要件

**数式再計算にはLibreOfficeが必要**: `recalc.py`スクリプトを使用して数式の値を再計算するためにLibreOfficeがインストールされていることを前提とします。スクリプトは初回実行時に自動的にLibreOfficeを設定します。

## データの読み取りと分析

### pandasによるデータ分析
データ分析、可視化、基本操作には、強力なデータ操作機能を提供する**pandas**を使用します：

```python
import pandas as pd

# Excelを読み込む
df = pd.read_excel('file.xlsx')  # デフォルト: 最初のシート
all_sheets = pd.read_excel('file.xlsx', sheet_name=None)  # すべてのシートを辞書として

# 分析
df.head()      # データのプレビュー
df.info()      # カラム情報
df.describe()  # 統計情報

# Excelに書き込む
df.to_excel('output.xlsx', index=False)
```

## Excelファイルのワークフロー

## 重要: ハードコードされた値ではなく数式を使用する

**常にPythonで値を計算してハードコードする代わりにExcelの数式を使用してください。** これにより、スプレッドシートが動的で更新可能な状態を維持できます。

### ❌ 間違い - 計算値のハードコード
```python
# 悪い例: Pythonで計算して結果をハードコード
total = df['Sales'].sum()
sheet['B10'] = total  # 5000をハードコード

# 悪い例: Pythonで成長率を計算
growth = (df.iloc[-1]['Revenue'] - df.iloc[0]['Revenue']) / df.iloc[0]['Revenue']
sheet['C5'] = growth  # 0.15をハードコード

# 悪い例: Pythonで平均を計算
avg = sum(values) / len(values)
sheet['D20'] = avg  # 42.5をハードコード
```

### ✅ 正しい - Excel数式の使用
```python
# 良い例: Excelに合計を計算させる
sheet['B10'] = '=SUM(B2:B9)'

# 良い例: 成長率をExcel数式として
sheet['C5'] = '=(C4-C2)/C2'

# 良い例: Excel関数で平均を計算
sheet['D20'] = '=AVERAGE(D2:D19)'
```

これはすべての計算に適用されます - 合計、パーセンテージ、比率、差分など。スプレッドシートはソースデータが変更されたときに再計算できる必要があります。

## 一般的なワークフロー
1. **ツールを選択**: データにはpandas、数式/書式設定にはopenpyxl
2. **作成/読み込み**: 新規ワークブックを作成するか既存ファイルを読み込む
3. **変更**: データ、数式、書式設定を追加/編集
4. **保存**: ファイルに書き込む
5. **数式を再計算（数式使用時は必須）**: recalc.pyスクリプトを使用
   ```bash
   python recalc.py output.xlsx
   ```
6. **エラーを確認して修正**:
   - スクリプトはエラーの詳細を含むJSONを返す
   - `status`が`errors_found`の場合、`error_summary`で具体的なエラータイプと場所を確認
   - 特定されたエラーを修正して再度計算
   - 修正すべき一般的なエラー:
     - `#REF!`: 無効なセル参照
     - `#DIV/0!`: ゼロ除算
     - `#VALUE!`: 数式で間違ったデータ型
     - `#NAME?`: 認識できない数式名

### 新規Excelファイルの作成

```python
# 数式と書式設定にopenpyxlを使用
from openpyxl import Workbook
from openpyxl.styles import Font, PatternFill, Alignment

wb = Workbook()
sheet = wb.active

# データを追加
sheet['A1'] = 'Hello'
sheet['B1'] = 'World'
sheet.append(['Row', 'of', 'data'])

# 数式を追加
sheet['B2'] = '=SUM(A1:A10)'

# 書式設定
sheet['A1'].font = Font(bold=True, color='FF0000')
sheet['A1'].fill = PatternFill('solid', start_color='FFFF00')
sheet['A1'].alignment = Alignment(horizontal='center')

# 列幅
sheet.column_dimensions['A'].width = 20

wb.save('output.xlsx')
```

### 既存Excelファイルの編集

```python
# 数式と書式設定を保持するためにopenpyxlを使用
from openpyxl import load_workbook

# 既存ファイルを読み込む
wb = load_workbook('existing.xlsx')
sheet = wb.active  # または特定のシートにwb['SheetName']

# 複数のシートを操作
for sheet_name in wb.sheetnames:
    sheet = wb[sheet_name]
    print(f"Sheet: {sheet_name}")

# セルを変更
sheet['A1'] = 'New Value'
sheet.insert_rows(2)  # 位置2に行を挿入
sheet.delete_cols(3)  # 列3を削除

# 新しいシートを追加
new_sheet = wb.create_sheet('NewSheet')
new_sheet['A1'] = 'Data'

wb.save('modified.xlsx')
```

## 数式の再計算

openpyxlで作成または変更されたExcelファイルには、数式が文字列として含まれていますが、計算された値は含まれていません。提供された`recalc.py`スクリプトを使用して数式を再計算してください：

```bash
python recalc.py <excel_file> [timeout_seconds]
```

例：
```bash
python recalc.py output.xlsx 30
```

スクリプトの機能：
- 初回実行時にLibreOfficeマクロを自動設定
- すべてのシートのすべての数式を再計算
- すべてのセルをスキャンしてExcelエラー（#REF!, #DIV/0!など）を検出
- エラーの場所と数を詳細に含むJSONを返す
- LinuxとmacOSの両方で動作

## 数式検証チェックリスト

数式が正しく機能することを確認するためのクイックチェック：

### 必須の検証
- [ ] **2-3のサンプル参照をテスト**: 完全なモデルを構築する前に正しい値を取得していることを確認
- [ ] **列マッピング**: Excel列が一致することを確認（例: 列64 = BL、BKではない）
- [ ] **行オフセット**: Excelの行は1インデックスであることを忘れずに（DataFrameの行5 = Excelの行6）

### 一般的な落とし穴
- [ ] **NaN処理**: `pd.notna()`でnull値をチェック
- [ ] **右端の列**: FYデータは50列以上の場合が多い
- [ ] **複数の一致**: 最初だけでなくすべての出現を検索
- [ ] **ゼロ除算**: 数式で`/`を使用する前に分母をチェック（#DIV/0!）
- [ ] **間違った参照**: すべてのセル参照が意図したセルを指していることを確認（#REF!）
- [ ] **シート間参照**: シート間をリンクする際は正しい形式を使用（Sheet1!A1）

### 数式テスト戦略
- [ ] **小さく始める**: 広く適用する前に2-3セルで数式をテスト
- [ ] **依存関係を確認**: 数式で参照されるすべてのセルが存在することをチェック
- [ ] **エッジケースをテスト**: ゼロ、負の値、非常に大きな値を含める

### recalc.py出力の解釈
スクリプトはエラーの詳細を含むJSONを返します：
```json
{
  "status": "success",           // または "errors_found"
  "total_errors": 0,              // エラーの総数
  "total_formulas": 42,           // ファイル内の数式の数
  "error_summary": {              // エラーが見つかった場合のみ存在
    "#REF!": {
      "count": 2,
      "locations": ["Sheet1!B5", "Sheet1!C10"]
    }
  }
}
```

## ベストプラクティス

### ライブラリの選択
- **pandas**: データ分析、一括操作、シンプルなデータエクスポートに最適
- **openpyxl**: 複雑な書式設定、数式、Excel固有の機能に最適

### openpyxlでの作業
- セルインデックスは1ベース（row=1, column=1はセルA1を指す）
- 計算された値を読み取るには`data_only=True`を使用: `load_workbook('file.xlsx', data_only=True)`
- **警告**: `data_only=True`で開いて保存すると、数式は値に置き換えられ永久に失われる
- 大きなファイルの場合: 読み取りには`read_only=True`、書き込みには`write_only=True`を使用
- 数式は保持されるが評価されない - 値を更新するにはrecalc.pyを使用

### pandasでの作業
- 推論の問題を避けるためにデータ型を指定: `pd.read_excel('file.xlsx', dtype={'id': str})`
- 大きなファイルの場合、特定の列を読み取る: `pd.read_excel('file.xlsx', usecols=['A', 'C', 'E'])`
- 日付を適切に処理: `pd.read_excel('file.xlsx', parse_dates=['date_column'])`

## コードスタイルガイドライン
**重要**: Excel操作用のPythonコードを生成する際：
- 不要なコメントなしで最小限の簡潔なPythonコードを書く
- 冗長な変数名や重複した操作を避ける
- 不要なprint文を避ける

**Excelファイル自体について**：
- 複雑な数式や重要な前提条件を含むセルにコメントを追加
- ハードコードされた値のデータソースを文書化
- 主要な計算とモデルセクションにメモを含める
