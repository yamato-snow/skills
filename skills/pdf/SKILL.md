---
name: pdf
description: テキストと表の抽出、新規PDFの作成、ドキュメントの結合/分割、フォーム処理のための包括的なPDF操作ツールキット。ClaudeがPDFフォームに入力したり、PDFドキュメントをプログラム的に処理、生成、大規模分析する必要がある場合に使用。
license: Proprietary. LICENSE.txt has complete terms
---

# PDF処理ガイド

## 概要

このガイドでは、Pythonライブラリとコマンドラインツールを使用した基本的なPDF処理操作を説明します。高度な機能、JavaScriptライブラリ、詳細な例についてはreference.mdを参照してください。PDFフォームに入力する必要がある場合は、forms.mdを読み、その指示に従ってください。

## クイックスタート

```python
from pypdf import PdfReader, PdfWriter

# PDFを読み込む
reader = PdfReader("document.pdf")
print(f"ページ数: {len(reader.pages)}")

# テキストを抽出
text = ""
for page in reader.pages:
    text += page.extract_text()
```

## Pythonライブラリ

### pypdf - 基本操作

#### PDFの結合
```python
from pypdf import PdfWriter, PdfReader

writer = PdfWriter()
for pdf_file in ["doc1.pdf", "doc2.pdf", "doc3.pdf"]:
    reader = PdfReader(pdf_file)
    for page in reader.pages:
        writer.add_page(page)

with open("merged.pdf", "wb") as output:
    writer.write(output)
```

#### PDFの分割
```python
reader = PdfReader("input.pdf")
for i, page in enumerate(reader.pages):
    writer = PdfWriter()
    writer.add_page(page)
    with open(f"page_{i+1}.pdf", "wb") as output:
        writer.write(output)
```

#### メタデータの抽出
```python
reader = PdfReader("document.pdf")
meta = reader.metadata
print(f"タイトル: {meta.title}")
print(f"作成者: {meta.author}")
print(f"件名: {meta.subject}")
print(f"作成アプリ: {meta.creator}")
```

#### ページの回転
```python
reader = PdfReader("input.pdf")
writer = PdfWriter()

page = reader.pages[0]
page.rotate(90)  # 時計回りに90度回転
writer.add_page(page)

with open("rotated.pdf", "wb") as output:
    writer.write(output)
```

### pdfplumber - テキストと表の抽出

#### レイアウト付きテキスト抽出
```python
import pdfplumber

with pdfplumber.open("document.pdf") as pdf:
    for page in pdf.pages:
        text = page.extract_text()
        print(text)
```

#### 表の抽出
```python
with pdfplumber.open("document.pdf") as pdf:
    for i, page in enumerate(pdf.pages):
        tables = page.extract_tables()
        for j, table in enumerate(tables):
            print(f"ページ{i+1}の表{j+1}:")
            for row in table:
                print(row)
```

#### 高度な表抽出
```python
import pandas as pd

with pdfplumber.open("document.pdf") as pdf:
    all_tables = []
    for page in pdf.pages:
        tables = page.extract_tables()
        for table in tables:
            if table:  # 表が空でないことを確認
                df = pd.DataFrame(table[1:], columns=table[0])
                all_tables.append(df)

# すべての表を結合
if all_tables:
    combined_df = pd.concat(all_tables, ignore_index=True)
    combined_df.to_excel("extracted_tables.xlsx", index=False)
```

### reportlab - PDF作成

#### 基本的なPDF作成
```python
from reportlab.lib.pagesizes import letter
from reportlab.pdfgen import canvas

c = canvas.Canvas("hello.pdf", pagesize=letter)
width, height = letter

# テキストを追加
c.drawString(100, height - 100, "Hello World!")
c.drawString(100, height - 120, "これはreportlabで作成したPDFです")

# 線を追加
c.line(100, height - 140, 400, height - 140)

# 保存
c.save()
```

#### 複数ページのPDF作成
```python
from reportlab.lib.pagesizes import letter
from reportlab.platypus import SimpleDocTemplate, Paragraph, Spacer, PageBreak
from reportlab.lib.styles import getSampleStyleSheet

doc = SimpleDocTemplate("report.pdf", pagesize=letter)
styles = getSampleStyleSheet()
story = []

# コンテンツを追加
title = Paragraph("レポートタイトル", styles['Title'])
story.append(title)
story.append(Spacer(1, 12))

body = Paragraph("これはレポートの本文です。" * 20, styles['Normal'])
story.append(body)
story.append(PageBreak())

# ページ2
story.append(Paragraph("ページ2", styles['Heading1']))
story.append(Paragraph("ページ2のコンテンツ", styles['Normal']))

# PDFを構築
doc.build(story)
```

## コマンドラインツール

### pdftotext (poppler-utils)
```bash
# テキストを抽出
pdftotext input.pdf output.txt

# レイアウトを保持してテキストを抽出
pdftotext -layout input.pdf output.txt

# 特定のページを抽出
pdftotext -f 1 -l 5 input.pdf output.txt  # ページ1-5
```

### qpdf
```bash
# PDFを結合
qpdf --empty --pages file1.pdf file2.pdf -- merged.pdf

# ページを分割
qpdf input.pdf --pages . 1-5 -- pages1-5.pdf
qpdf input.pdf --pages . 6-10 -- pages6-10.pdf

# ページを回転
qpdf input.pdf output.pdf --rotate=+90:1  # ページ1を90度回転

# パスワードを解除
qpdf --password=mypassword --decrypt encrypted.pdf decrypted.pdf
```

### pdftk（利用可能な場合）
```bash
# 結合
pdftk file1.pdf file2.pdf cat output merged.pdf

# 分割
pdftk input.pdf burst

# 回転
pdftk input.pdf rotate 1east output rotated.pdf
```

## 一般的なタスク

### スキャンされたPDFからテキストを抽出
```python
# 必要: pip install pytesseract pdf2image
import pytesseract
from pdf2image import convert_from_path

# PDFを画像に変換
images = convert_from_path('scanned.pdf')

# 各ページをOCR
text = ""
for i, image in enumerate(images):
    text += f"ページ {i+1}:\n"
    text += pytesseract.image_to_string(image)
    text += "\n\n"

print(text)
```

### 透かしを追加
```python
from pypdf import PdfReader, PdfWriter

# 透かしを作成（または既存のものを読み込む）
watermark = PdfReader("watermark.pdf").pages[0]

# すべてのページに適用
reader = PdfReader("document.pdf")
writer = PdfWriter()

for page in reader.pages:
    page.merge_page(watermark)
    writer.add_page(page)

with open("watermarked.pdf", "wb") as output:
    writer.write(output)
```

### 画像を抽出
```bash
# pdfimages (poppler-utils)を使用
pdfimages -j input.pdf output_prefix

# これによりoutput_prefix-000.jpg、output_prefix-001.jpgなどとして画像が抽出されます
```

### パスワード保護
```python
from pypdf import PdfReader, PdfWriter

reader = PdfReader("input.pdf")
writer = PdfWriter()

for page in reader.pages:
    writer.add_page(page)

# パスワードを追加
writer.encrypt("userpassword", "ownerpassword")

with open("encrypted.pdf", "wb") as output:
    writer.write(output)
```

## クイックリファレンス

| タスク | 最適なツール | コマンド/コード |
|------|-----------|--------------|
| PDFを結合 | pypdf | `writer.add_page(page)` |
| PDFを分割 | pypdf | 1ページずつファイルに |
| テキストを抽出 | pdfplumber | `page.extract_text()` |
| 表を抽出 | pdfplumber | `page.extract_tables()` |
| PDFを作成 | reportlab | CanvasまたはPlatypus |
| コマンドラインで結合 | qpdf | `qpdf --empty --pages ...` |
| スキャンPDFをOCR | pytesseract | まず画像に変換 |
| PDFフォームに入力 | pdf-libまたはpypdf（forms.md参照） | forms.mdを参照 |

## 次のステップ

- 高度なpypdfium2の使用については、reference.mdを参照
- JavaScriptライブラリ（pdf-lib）については、reference.mdを参照
- PDFフォームに入力する必要がある場合は、forms.mdの指示に従う
- トラブルシューティングガイドについては、reference.mdを参照
