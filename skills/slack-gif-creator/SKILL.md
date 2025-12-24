---
name: slack-gif-creator
description: Slack向けに最適化されたアニメーションGIFを作成するための知識とユーティリティ。制約、検証ツール、アニメーションコンセプトを提供。ユーザーが「Slack用にXがYをしているGIFを作って」のようなSlack向けアニメーションGIFをリクエストした場合に使用。
license: Complete terms in LICENSE.txt
---

# Slack GIF Creator

Slack向けに最適化されたアニメーションGIFを作成するためのユーティリティと知識を提供するツールキット。

## Slack要件

**寸法：**
- 絵文字GIF：128x128（推奨）
- メッセージGIF：480x480

**パラメータ：**
- FPS：10-30（低いほどファイルサイズが小さい）
- 色数：48-128（少ないほどファイルサイズが小さい）
- 長さ：絵文字GIFは3秒未満に

## コアワークフロー

```python
from core.gif_builder import GIFBuilder
from PIL import Image, ImageDraw

# 1. ビルダーを作成
builder = GIFBuilder(width=128, height=128, fps=10)

# 2. フレームを生成
for i in range(12):
    frame = Image.new('RGB', (128, 128), (240, 248, 255))
    draw = ImageDraw.Draw(frame)

    # PILプリミティブを使用してアニメーションを描画
    # （円、ポリゴン、線など）

    builder.add_frame(frame)

# 3. 最適化して保存
builder.save('output.gif', num_colors=48, optimize_for_emoji=True)
```

## グラフィックの描画

### ユーザーアップロード画像の操作
ユーザーが画像をアップロードした場合、彼らが何を望んでいるか検討：
- **直接使用**（例：「これをアニメーション化」「これをフレームに分割」）
- **インスピレーションとして使用**（例：「これに似たものを作って」）

PILを使用して画像を読み込み操作：
```python
from PIL import Image

uploaded = Image.open('file.png')
# 直接使用、または色/スタイルの参考として
```

### ゼロから描画
ゼロからグラフィックを描画する場合、PIL ImageDrawプリミティブを使用：

```python
from PIL import ImageDraw

draw = ImageDraw.Draw(frame)

# 円/楕円
draw.ellipse([x1, y1, x2, y2], fill=(r, g, b), outline=(r, g, b), width=3)

# 星、三角形、任意のポリゴン
points = [(x1, y1), (x2, y2), (x3, y3), ...]
draw.polygon(points, fill=(r, g, b), outline=(r, g, b), width=3)

# 線
draw.line([(x1, y1), (x2, y2)], fill=(r, g, b), width=5)

# 長方形
draw.rectangle([x1, y1, x2, y2], fill=(r, g, b), outline=(r, g, b), width=3)
```

**使用しない：** 絵文字フォント（プラットフォーム間で信頼性が低い）や、このスキルにパッケージ済みグラフィックがあると仮定すること。

### グラフィックを美しく見せる

グラフィックは基本的ではなく、洗練されてクリエイティブに見えるべき。方法は以下：

**太い線を使用** - 輪郭と線には常に`width=2`以上を設定。細い線（width=1）はギザギザでアマチュアっぽく見える。

**視覚的な奥行きを追加**：
- 背景にグラデーションを使用（`create_gradient_background`）
- 複雑さのために複数のシェイプをレイヤー化（例：内側に小さな星がある星）

**シェイプをより興味深くする**：
- 単純な円を描くだけでなく、ハイライト、リング、パターンを追加
- 星にはグロー効果（背後に大きく半透明のバージョンを描画）
- 複数のシェイプを組み合わせる（星＋きらめき、円＋リング）

**色に注意を払う**：
- 鮮やかで補色的な色を使用
- コントラストを追加（明るいシェイプには暗い輪郭、暗いシェイプには明るい輪郭）
- 全体の構成を考慮

**複雑なシェイプ**（ハート、雪の結晶など）の場合：
- ポリゴンと楕円の組み合わせを使用
- 対称性のためにポイントを慎重に計算
- ディテールを追加（ハートにはハイライトカーブ、雪の結晶には複雑な枝）

クリエイティブで細部まで！良いSlack GIFは、プレースホルダーグラフィックではなく、洗練されて見えるべき。

## 利用可能なユーティリティ

### GIFBuilder (`core.gif_builder`)
フレームを組み立てSlack向けに最適化：
```python
builder = GIFBuilder(width=128, height=128, fps=10)
builder.add_frame(frame)  # PIL Imageを追加
builder.add_frames(frames)  # フレームのリストを追加
builder.save('out.gif', num_colors=48, optimize_for_emoji=True, remove_duplicates=True)
```

### バリデータ (`core.validators`)
GIFがSlack要件を満たすか確認：
```python
from core.validators import validate_gif, is_slack_ready

# 詳細な検証
passes, info = validate_gif('my.gif', is_emoji=True, verbose=True)

# クイックチェック
if is_slack_ready('my.gif'):
    print("準備完了!")
```

### イージング関数 (`core.easing`)
リニアではなくスムーズなモーション：
```python
from core.easing import interpolate

# 0.0から1.0への進行
t = i / (num_frames - 1)

# イージングを適用
y = interpolate(start=0, end=400, t=t, easing='ease_out')

# 利用可能：linear, ease_in, ease_out, ease_in_out,
#           bounce_out, elastic_out, back_out
```

### フレームヘルパー (`core.frame_composer`)
一般的なニーズのための便利な関数：
```python
from core.frame_composer import (
    create_blank_frame,         # ソリッドカラー背景
    create_gradient_background,  # 垂直グラデーション
    draw_circle,                # 円のヘルパー
    draw_text,                  # シンプルなテキストレンダリング
    draw_star                   # 5角星
)
```

## アニメーションコンセプト

### シェイク/バイブレート
オシレーションでオブジェクト位置をオフセット：
- フレームインデックスで`math.sin()`または`math.cos()`を使用
- 自然な感触のために小さなランダム変動を追加
- x位置および/またはy位置に適用

### パルス/ハートビート
オブジェクトサイズをリズミカルにスケール：
- スムーズなパルスには`math.sin(t * frequency * 2 * math.pi)`を使用
- ハートビートの場合：2回の素早いパルスの後に一時停止（正弦波を調整）
- ベースサイズの0.8から1.2の間でスケール

### バウンス
オブジェクトが落下してバウンス：
- 着地には`interpolate()`で`easing='bounce_out'`を使用
- 落下（加速）には`easing='ease_in'`を使用
- 各フレームでy速度を増加させて重力を適用

### スピン/回転
オブジェクトを中心周りで回転：
- PIL：`image.rotate(angle, resample=Image.BICUBIC)`
- 揺れの場合：リニアではなく角度に正弦波を使用

### フェードイン/アウト
徐々に出現または消失：
- RGBA画像を作成し、アルファチャンネルを調整
- または`Image.blend(image1, image2, alpha)`を使用
- フェードイン：アルファを0から1へ
- フェードアウト：アルファを1から0へ

### スライド
オブジェクトを画面外から位置へ移動：
- 開始位置：フレーム境界外
- 終了位置：ターゲット位置
- スムーズな停止には`interpolate()`で`easing='ease_out'`を使用
- オーバーシュートには`easing='back_out'`を使用

### ズーム
ズーム効果のためにスケールと位置を調整：
- ズームイン：0.1から2.0にスケール、中央をクロップ
- ズームアウト：2.0から1.0にスケール
- ドラマのためにモーションブラーを追加可能（PILフィルター）

### エクスプロード/パーティクルバースト
外向きに放射するパーティクルを作成：
- ランダムな角度と速度でパーティクルを生成
- 各パーティクルを更新：`x += vx`、`y += vy`
- 重力を追加：`vy += gravity_constant`
- 時間とともにパーティクルをフェードアウト（アルファを減少）

## 最適化戦略

ファイルサイズを小さくするよう依頼された場合のみ、以下のいくつかの方法を実装：

1. **フレーム数を減らす** - 低いFPS（20ではなく10）または短い長さ
2. **色数を減らす** - 128ではなく`num_colors=48`
3. **寸法を小さく** - 480x480ではなく128x128
4. **重複を削除** - save()で`remove_duplicates=True`
5. **絵文字モード** - `optimize_for_emoji=True`で自動最適化

```python
# 絵文字の最大最適化
builder.save(
    'emoji.gif',
    num_colors=48,
    optimize_for_emoji=True,
    remove_duplicates=True
)
```

## 哲学

このスキルが提供するもの：
- **知識**：Slackの要件とアニメーションコンセプト
- **ユーティリティ**：GIFBuilder、バリデータ、イージング関数
- **柔軟性**：PILプリミティブを使用してアニメーションロジックを作成

このスキルが提供しないもの：
- 固定のアニメーションテンプレートや事前作成された関数
- 絵文字フォントレンダリング（プラットフォーム間で信頼性が低い）
- スキルに組み込まれたパッケージ済みグラフィックのライブラリ

**ユーザーアップロードに関する注意**：このスキルは事前構築されたグラフィックを含まないが、ユーザーが画像をアップロードした場合は、PILを使用して読み込み操作する - リクエストに基づいて直接使用するか、インスピレーションとしてのみ使用するか解釈する。

クリエイティブに！コンセプトを組み合わせ（バウンス＋回転、パルス＋スライドなど）、PILの全機能を使用する。

## 依存関係

```bash
pip install pillow imageio numpy
```
