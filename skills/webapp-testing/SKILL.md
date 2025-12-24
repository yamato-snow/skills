---
name: webapp-testing
description: Playwrightを使用してローカルWebアプリケーションとのインタラクションおよびテストを行うためのツールキット。フロントエンド機能の検証、UIの動作デバッグ、ブラウザスクリーンショットのキャプチャ、ブラウザログの表示をサポート。
license: Complete terms in LICENSE.txt
---

# Webアプリケーションテスト

ローカルWebアプリケーションをテストするには、ネイティブPython Playwrightスクリプトを記述。

**利用可能なヘルパースクリプト**：
- `scripts/with_server.py` - サーバーライフサイクルを管理（複数サーバーをサポート）

**スクリプトは必ず最初に`--help`で実行**して使用方法を確認。カスタマイズされたソリューションが絶対に必要であることがわかるまでソースを読まない。これらのスクリプトは非常に大きくなる可能性があり、コンテキストウィンドウを汚染する。コンテキストウィンドウに取り込むのではなく、ブラックボックススクリプトとして直接呼び出すために存在する。

## 決定ツリー：アプローチの選択

```
ユーザータスク → 静的HTMLか？
    ├─ はい → HTMLファイルを直接読んでセレクターを特定
    │         ├─ 成功 → セレクターを使用してPlaywrightスクリプトを記述
    │         └─ 失敗/不完全 → 動的として扱う（下記）
    │
    └─ いいえ（動的webapp）→ サーバーはすでに実行中か？
        ├─ いいえ → 実行：python scripts/with_server.py --help
        │        次にヘルパーを使用 + 簡略化されたPlaywrightスクリプトを記述
        │
        └─ はい → 偵察してからアクション：
            1. ナビゲートしてnetworkidleを待機
            2. スクリーンショットを撮るかDOMを検査
            3. レンダリングされた状態からセレクターを特定
            4. 発見したセレクターでアクションを実行
```

## 例：with_server.pyの使用

サーバーを起動するには、まず`--help`を実行し、次にヘルパーを使用：

**単一サーバー：**
```bash
python scripts/with_server.py --server "npm run dev" --port 5173 -- python your_automation.py
```

**複数サーバー（例：バックエンド + フロントエンド）：**
```bash
python scripts/with_server.py \
  --server "cd backend && python server.py" --port 3000 \
  --server "cd frontend && npm run dev" --port 5173 \
  -- python your_automation.py
```

自動化スクリプトを作成するには、Playwrightロジックのみを含める（サーバーは自動的に管理される）：
```python
from playwright.sync_api import sync_playwright

with sync_playwright() as p:
    browser = p.chromium.launch(headless=True) # 常にheadlessモードでchromiumを起動
    page = browser.new_page()
    page.goto('http://localhost:5173') # サーバーはすでに実行中で準備完了
    page.wait_for_load_state('networkidle') # 重要：JSの実行を待機
    # ... 自動化ロジック
    browser.close()
```

## 偵察してからアクションパターン

1. **レンダリングされたDOMを検査**：
   ```python
   page.screenshot(path='/tmp/inspect.png', full_page=True)
   content = page.content()
   page.locator('button').all()
   ```

2. 検査結果から**セレクターを特定**

3. 発見したセレクターを使用して**アクションを実行**

## よくある落とし穴

❌ 動的アプリで`networkidle`を待機する前にDOMを検査**しない**
✅ 検査前に`page.wait_for_load_state('networkidle')`を待機**する**

## ベストプラクティス

- **バンドルされたスクリプトをブラックボックスとして使用** - タスクを達成するには、`scripts/`で利用可能なスクリプトの1つが役立つかどうかを検討。これらのスクリプトは、コンテキストウィンドウを乱すことなく、一般的で複雑なワークフローを確実に処理。使用方法を確認するには`--help`を使用し、直接呼び出す。
- 同期スクリプトには`sync_playwright()`を使用
- 完了時は常にブラウザを閉じる
- 説明的なセレクターを使用：`text=`、`role=`、CSSセレクター、またはID
- 適切な待機を追加：`page.wait_for_selector()`または`page.wait_for_timeout()`

## リファレンスファイル

- **examples/** - 一般的なパターンを示す例：
  - `element_discovery.py` - ページ上のボタン、リンク、入力を発見
  - `static_html_automation.py` - ローカルHTMLにfile:// URLを使用
  - `console_logging.py` - 自動化中のコンソールログのキャプチャ
