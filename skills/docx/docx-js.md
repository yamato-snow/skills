# DOCXライブラリチュートリアル

JavaScript/TypeScriptで.docxファイルを生成。

**重要：開始前にこのドキュメント全体を読んでください。** 重要なフォーマットルールと一般的な落とし穴が全体を通してカバーされています - セクションをスキップすると、破損したファイルやレンダリングの問題が発生する可能性があります。

## セットアップ
docxがすでにグローバルにインストールされていることを前提
インストールされていない場合：`npm install -g docx`

```javascript
const { Document, Packer, Paragraph, TextRun, Table, TableRow, TableCell, ImageRun, Media,
        Header, Footer, AlignmentType, PageOrientation, LevelFormat, ExternalHyperlink,
        InternalHyperlink, TableOfContents, HeadingLevel, BorderStyle, WidthType, TabStopType,
        TabStopPosition, UnderlineType, ShadingType, VerticalAlign, SymbolRun, PageNumber,
        FootnoteReferenceRun, Footnote, PageBreak } = require('docx');

// 作成と保存
const doc = new Document({ sections: [{ children: [/* コンテンツ */] }] });
Packer.toBuffer(doc).then(buffer => fs.writeFileSync("doc.docx", buffer)); // Node.js
Packer.toBlob(doc).then(blob => { /* ダウンロードロジック */ }); // ブラウザ
```

## テキストとフォーマット
```javascript
// 重要：改行に\nを使用しない - 常に別々のParagraph要素を使用
// ❌ 間違い：new TextRun("Line 1\nLine 2")
// ✅ 正解：new Paragraph({ children: [new TextRun("Line 1")] }), new Paragraph({ children: [new TextRun("Line 2")] })

// すべてのフォーマットオプションを含む基本テキスト
new Paragraph({
  alignment: AlignmentType.CENTER,
  spacing: { before: 200, after: 200 },
  indent: { left: 720, right: 720 },
  children: [
    new TextRun({ text: "Bold", bold: true }),
    new TextRun({ text: "Italic", italics: true }),
    new TextRun({ text: "Underlined", underline: { type: UnderlineType.DOUBLE, color: "FF0000" } }),
    new TextRun({ text: "Colored", color: "FF0000", size: 28, font: "Arial" }), // Arialがデフォルト
    new TextRun({ text: "Highlighted", highlight: "yellow" }),
    new TextRun({ text: "Strikethrough", strike: true }),
    new TextRun({ text: "x2", superScript: true }),
    new TextRun({ text: "H2O", subScript: true }),
    new TextRun({ text: "SMALL CAPS", smallCaps: true }),
    new SymbolRun({ char: "2022", font: "Symbol" }), // 箇条書き •
    new SymbolRun({ char: "00A9", font: "Arial" })   // 著作権 © - シンボルにはArial
  ]
})
```

## スタイルとプロフェッショナルなフォーマット

```javascript
const doc = new Document({
  styles: {
    default: { document: { run: { font: "Arial", size: 24 } } }, // 12ptデフォルト
    paragraphStyles: [
      // ドキュメントタイトルスタイル - 組み込みTitleスタイルをオーバーライド
      { id: "Title", name: "Title", basedOn: "Normal",
        run: { size: 56, bold: true, color: "000000", font: "Arial" },
        paragraph: { spacing: { before: 240, after: 120 }, alignment: AlignmentType.CENTER } },
      // 重要：組み込みの見出しスタイルをオーバーライドするには正確なIDを使用
      { id: "Heading1", name: "Heading 1", basedOn: "Normal", next: "Normal", quickFormat: true,
        run: { size: 32, bold: true, color: "000000", font: "Arial" }, // 16pt
        paragraph: { spacing: { before: 240, after: 240 }, outlineLevel: 0 } }, // TOCに必須
      { id: "Heading2", name: "Heading 2", basedOn: "Normal", next: "Normal", quickFormat: true,
        run: { size: 28, bold: true, color: "000000", font: "Arial" }, // 14pt
        paragraph: { spacing: { before: 180, after: 180 }, outlineLevel: 1 } },
      // カスタムスタイルは独自のIDを使用
      { id: "myStyle", name: "My Style", basedOn: "Normal",
        run: { size: 28, bold: true, color: "000000" },
        paragraph: { spacing: { after: 120 }, alignment: AlignmentType.CENTER } }
    ],
    characterStyles: [{ id: "myCharStyle", name: "My Char Style",
      run: { color: "FF0000", bold: true, underline: { type: UnderlineType.SINGLE } } }]
  },
  sections: [{
    properties: { page: { margin: { top: 1440, right: 1440, bottom: 1440, left: 1440 } } },
    children: [
      new Paragraph({ heading: HeadingLevel.TITLE, children: [new TextRun("Document Title")] }), // オーバーライドされたTitleスタイルを使用
      new Paragraph({ heading: HeadingLevel.HEADING_1, children: [new TextRun("Heading 1")] }), // オーバーライドされたHeading1スタイルを使用
      new Paragraph({ style: "myStyle", children: [new TextRun("Custom paragraph style")] }),
      new Paragraph({ children: [
        new TextRun("Normal with "),
        new TextRun({ text: "custom char style", style: "myCharStyle" })
      ]})
    ]
  }]
});
```

**プロフェッショナルなフォント組み合わせ：**
- **Arial（見出し）+ Arial（本文）** - 最も広くサポートされ、クリーンでプロフェッショナル
- **Times New Roman（見出し）+ Arial（本文）** - クラシックなセリフ見出しとモダンなサンセリフ本文
- **Georgia（見出し）+ Verdana（本文）** - 画面読み取りに最適化、エレガントなコントラスト

**主要なスタイリング原則：**
- **組み込みスタイルをオーバーライド**："Heading1"、"Heading2"、"Heading3"などの正確なIDを使用してWordの組み込み見出しスタイルをオーバーライド
- **HeadingLevel定数**：`HeadingLevel.HEADING_1`は"Heading1"スタイルを使用、`HeadingLevel.HEADING_2`は"Heading2"スタイルを使用、など
- **outlineLevelを含める**：TOCが正しく機能するようにH1には`outlineLevel: 0`、H2には`outlineLevel: 1`などを設定
- 一貫性のためにインラインフォーマットではなく**カスタムスタイルを使用**
- `styles.default.document.run.font`を使用して**デフォルトフォントを設定** - Arialが広くサポートされている
- 異なるフォントサイズで**視覚的階層を確立**（タイトル > 見出し > 本文）
- `before`と`after`の段落スペーシングで**適切な間隔を追加**
- **色は控えめに使用**：タイトルと見出し（heading 1、heading 2など）にはデフォルトで黒（000000）とグレーのシェードを使用
- **一貫したマージンを設定**（1440 = 1インチが標準）


## リスト（常に適切なリストを使用 - UNICODEの箇条書きは絶対に使用しない）
```javascript
// 箇条書き - 常にnumbering設定を使用、UNICODEシンボルは使用しない
// 重要：LevelFormat.BULLET定数を使用、文字列"bullet"は使用しない
const doc = new Document({
  numbering: {
    config: [
      { reference: "bullet-list",
        levels: [{ level: 0, format: LevelFormat.BULLET, text: "•", alignment: AlignmentType.LEFT,
          style: { paragraph: { indent: { left: 720, hanging: 360 } } } }] },
      { reference: "first-numbered-list",
        levels: [{ level: 0, format: LevelFormat.DECIMAL, text: "%1.", alignment: AlignmentType.LEFT,
          style: { paragraph: { indent: { left: 720, hanging: 360 } } } }] },
      { reference: "second-numbered-list", // 異なるリファレンス = 1から再開
        levels: [{ level: 0, format: LevelFormat.DECIMAL, text: "%1.", alignment: AlignmentType.LEFT,
          style: { paragraph: { indent: { left: 720, hanging: 360 } } } }] }
    ]
  },
  sections: [{
    children: [
      // 箇条書きリスト項目
      new Paragraph({ numbering: { reference: "bullet-list", level: 0 },
        children: [new TextRun("First bullet point")] }),
      new Paragraph({ numbering: { reference: "bullet-list", level: 0 },
        children: [new TextRun("Second bullet point")] }),
      // 番号付きリスト項目
      new Paragraph({ numbering: { reference: "first-numbered-list", level: 0 },
        children: [new TextRun("First numbered item")] }),
      new Paragraph({ numbering: { reference: "first-numbered-list", level: 0 },
        children: [new TextRun("Second numbered item")] }),
      // ⚠️ 重要：異なるリファレンス = 1から再開する独立したリスト
      // 同じリファレンス = 前の番号を継続
      new Paragraph({ numbering: { reference: "second-numbered-list", level: 0 },
        children: [new TextRun("Starts at 1 again (because different reference)")] })
    ]
  }]
});

// ⚠️ 重要な番号付けルール：各リファレンスは独立した番号付きリストを作成
// - 同じリファレンス = 番号を継続（1, 2, 3... その後 4, 5, 6...）
// - 異なるリファレンス = 1から再開（1, 2, 3... その後 1, 2, 3...）
// 各番号付きセクションには一意のリファレンス名を使用！

// ⚠️ 重要：UNICODEの箇条書きは絶対に使用しない - 正しく機能しない偽のリストを作成する
// new TextRun("• Item")           // 間違い
// new SymbolRun({ char: "2022" }) // 間違い
// ✅ 常に実際のWordリストにはLevelFormat.BULLETを使用したnumbering設定を使用
```

## テーブル
```javascript
// マージン、ボーダー、ヘッダー、箇条書きを含む完全なテーブル
const tableBorder = { style: BorderStyle.SINGLE, size: 1, color: "CCCCCC" };
const cellBorders = { top: tableBorder, bottom: tableBorder, left: tableBorder, right: tableBorder };

new Table({
  columnWidths: [4680, 4680], // ⚠️ 重要：テーブルレベルで列幅を設定 - 値はDXA（1ポイントの20分の1）
  margins: { top: 100, bottom: 100, left: 180, right: 180 }, // すべてのセルに対して一度設定
  rows: [
    new TableRow({
      tableHeader: true,
      children: [
        new TableCell({
          borders: cellBorders,
          width: { size: 4680, type: WidthType.DXA }, // 各セルにも幅を設定
          // ⚠️ 重要：Wordで黒い背景を防ぐために常にShadingType.CLEARを使用
          shading: { fill: "D5E8F0", type: ShadingType.CLEAR },
          verticalAlign: VerticalAlign.CENTER,
          children: [new Paragraph({
            alignment: AlignmentType.CENTER,
            children: [new TextRun({ text: "Header", bold: true, size: 22 })]
          })]
        }),
        new TableCell({
          borders: cellBorders,
          width: { size: 4680, type: WidthType.DXA }, // 各セルにも幅を設定
          shading: { fill: "D5E8F0", type: ShadingType.CLEAR },
          children: [new Paragraph({
            alignment: AlignmentType.CENTER,
            children: [new TextRun({ text: "Bullet Points", bold: true, size: 22 })]
          })]
        })
      ]
    }),
    new TableRow({
      children: [
        new TableCell({
          borders: cellBorders,
          width: { size: 4680, type: WidthType.DXA }, // 各セルにも幅を設定
          children: [new Paragraph({ children: [new TextRun("Regular data")] })]
        }),
        new TableCell({
          borders: cellBorders,
          width: { size: 4680, type: WidthType.DXA }, // 各セルにも幅を設定
          children: [
            new Paragraph({
              numbering: { reference: "bullet-list", level: 0 },
              children: [new TextRun("First bullet point")]
            }),
            new Paragraph({
              numbering: { reference: "bullet-list", level: 0 },
              children: [new TextRun("Second bullet point")]
            })
          ]
        })
      ]
    })
  ]
})
```

**重要：テーブルの幅とボーダー**
- `columnWidths: [width1, width2, ...]`配列と各セルの`width: { size: X, type: WidthType.DXA }`の両方を使用
- 値はDXA（1ポイントの20分の1）：1440 = 1インチ、レターサイズの使用可能幅 = 9360 DXA（1インチマージン）
- ボーダーは`Table`自体ではなく個々の`TableCell`要素に適用

**事前計算された列幅（1インチマージンのレターサイズ = 合計9360 DXA）：**
- **2列：** `columnWidths: [4680, 4680]`（等幅）
- **3列：** `columnWidths: [3120, 3120, 3120]`（等幅）

## リンクとナビゲーション
```javascript
// TOC（見出しが必要） - 重要：カスタムスタイルではなくHeadingLevelのみを使用
// ❌ 間違い：new Paragraph({ heading: HeadingLevel.HEADING_1, style: "customHeader", children: [new TextRun("Title")] })
// ✅ 正解：new Paragraph({ heading: HeadingLevel.HEADING_1, children: [new TextRun("Title")] })
new TableOfContents("Table of Contents", { hyperlink: true, headingStyleRange: "1-3" }),

// 外部リンク
new Paragraph({
  children: [new ExternalHyperlink({
    children: [new TextRun({ text: "Google", style: "Hyperlink" })],
    link: "https://www.google.com"
  })]
}),

// 内部リンクとブックマーク
new Paragraph({
  children: [new InternalHyperlink({
    children: [new TextRun({ text: "Go to Section", style: "Hyperlink" })],
    anchor: "section1"
  })]
}),
new Paragraph({
  children: [new TextRun("Section Content")],
  bookmark: { id: "section1", name: "section1" }
}),
```

## 画像とメディア
```javascript
// サイズと位置指定を含む基本的な画像
// 重要：常に'type'パラメータを指定 - ImageRunに必須
new Paragraph({
  alignment: AlignmentType.CENTER,
  children: [new ImageRun({
    type: "png", // 新要件：画像タイプを指定する必要がある（png, jpg, jpeg, gif, bmp, svg）
    data: fs.readFileSync("image.png"),
    transformation: { width: 200, height: 150, rotation: 0 }, // rotationは度数
    altText: { title: "Logo", description: "Company logo", name: "Name" } // 重要：3つのフィールドすべてが必須
  })]
})
```

## ページブレイク
```javascript
// 手動ページブレイク
new Paragraph({ children: [new PageBreak()] }),

// 段落前のページブレイク
new Paragraph({
  pageBreakBefore: true,
  children: [new TextRun("This starts on a new page")]
})

// ⚠️ 重要：PageBreakを単独で使用しない - Wordが開けない無効なXMLを作成する
// ❌ 間違い：new PageBreak()
// ✅ 正解：new Paragraph({ children: [new PageBreak()] })
```

## ヘッダー/フッターとページ設定
```javascript
const doc = new Document({
  sections: [{
    properties: {
      page: {
        margin: { top: 1440, right: 1440, bottom: 1440, left: 1440 }, // 1440 = 1インチ
        size: { orientation: PageOrientation.LANDSCAPE },
        pageNumbers: { start: 1, formatType: "decimal" } // "upperRoman", "lowerRoman", "upperLetter", "lowerLetter"
      }
    },
    headers: {
      default: new Header({ children: [new Paragraph({
        alignment: AlignmentType.RIGHT,
        children: [new TextRun("Header Text")]
      })] })
    },
    footers: {
      default: new Footer({ children: [new Paragraph({
        alignment: AlignmentType.CENTER,
        children: [new TextRun("Page "), new TextRun({ children: [PageNumber.CURRENT] }), new TextRun(" of "), new TextRun({ children: [PageNumber.TOTAL_PAGES] })]
      })] })
    },
    children: [/* コンテンツ */]
  }]
});
```

## タブ
```javascript
new Paragraph({
  tabStops: [
    { type: TabStopType.LEFT, position: TabStopPosition.MAX / 4 },
    { type: TabStopType.CENTER, position: TabStopPosition.MAX / 2 },
    { type: TabStopType.RIGHT, position: TabStopPosition.MAX * 3 / 4 }
  ],
  children: [new TextRun("Left\tCenter\tRight")]
})
```

## 定数とクイックリファレンス
- **下線：** `SINGLE`, `DOUBLE`, `WAVY`, `DASH`
- **ボーダー：** `SINGLE`, `DOUBLE`, `DASHED`, `DOTTED`
- **番号付け：** `DECIMAL` (1,2,3), `UPPER_ROMAN` (I,II,III), `LOWER_LETTER` (a,b,c)
- **タブ：** `LEFT`, `CENTER`, `RIGHT`, `DECIMAL`
- **シンボル：** `"2022"` (•), `"00A9"` (©), `"00AE"` (®), `"2122"` (™), `"00B0"` (°), `"F070"` (✓), `"F0FC"` (✗)

## 重大な問題と一般的なミス
- **重要：PageBreakは常にParagraph内に必要** - 単独のPageBreakはWordが開けない無効なXMLを作成
- **テーブルセルのシェーディングには常にShadingType.CLEARを使用** - ShadingType.SOLIDは使用しない（黒い背景になる）
- 測定はDXA（1440 = 1インチ）| 各テーブルセルには1つ以上のParagraphが必要 | TOCにはHeadingLevelスタイルのみ必要
- プロフェッショナルな外観と適切な視覚的階層のために**常にArialフォントのカスタムスタイルを使用**
- `styles.default.document.run.font`を使用して**常にデフォルトフォントを設定** - Arial推奨
- 互換性のためにテーブルには**常にcolumnWidths配列を使用** + 個々のセル幅
- **箇条書きにUNICODEシンボルは絶対に使用しない** - 常に`LevelFormat.BULLET`定数（文字列"bullet"ではない）を使用した適切なnumbering設定を使用
- **どこでも改行に\nは絶対に使用しない** - 常に各行に別々のParagraph要素を使用
- **Paragraphのchildren内では常にTextRunオブジェクトを使用** - Paragraphに直接textプロパティを使用しない
- **画像の重要事項**：ImageRunには`type`パラメータが必須 - 常に"png", "jpg", "jpeg", "gif", "bmp", または"svg"を指定
- **箇条書きの重要事項**：`LevelFormat.BULLET`定数を使用し、文字列"bullet"は使用しない、また箇条書き文字のために`text: "•"`を含める必要がある
- **番号付けの重要事項**：各numberingリファレンスは独立したリストを作成。同じリファレンス = 番号を継続（1,2,3 その後 4,5,6）。異なるリファレンス = 1から再開（1,2,3 その後 1,2,3）。各番号付きセクションには一意のリファレンス名を使用！
- **TOCの重要事項**：TableOfContentsを使用する場合、見出しはHeadingLevelのみを使用する必要がある - 見出し段落にカスタムスタイルを追加するとTOCが壊れる
- **テーブル**：`columnWidths`配列 + 個々のセル幅を設定、ボーダーはテーブルではなくセルに適用
- 一貫したセルパディングのために**テーブルマージンはTABLEレベルで設定**（セルごとの繰り返しを避ける）
