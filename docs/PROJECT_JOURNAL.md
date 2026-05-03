# PROJECT JOURNAL — Ouchi Colon ころころレース v2

---

## Phase 1 — 初期プロトタイプ (2026-05-04)

### 目標
GitHub Pages で公開できる `index.html` 単体ゲームの初版を作る。

### 実装内容

#### スタート画面
- おもちゃ部屋・リビング背景を Canvas で描画（窓・本棚・植物・テディベア・三角コーン・チェッカーフラグ・楕円トラック）
- タイトルロゴ（CSS テキスト）
- キャラクター7体を Canvas で配置（MIKARO が中央大表示）
- スタート / キャラクター / コース / せってい ボタン
- ランキング / おしらせ下部バー

#### キャラクター選択画面
- 選択中キャラの大カード（Canvas 描画、星評価・説明文）
- 8キャラのグリッドサムネイル（Canvas 描画）
- MIKARO のみ選択可、他は 🔒 ロック表示
- もどる / けってい / せつめい ボタン

#### プレイ画面（疑似3D）
- スーパーファミコン・マリオカート風の射影レンダリング
  - ストライプ法（縞模様で奥行きを表現）
  - 赤白カーブ縁石、中央破線
  - perspective 関数で奥行きに応じてオブジェクトのサイズが変化
- リビング背景（Canvas 描画）: ソファ・窓・本棚・犬・植物
- MIKARO 背面ビュー（プレイヤー）
- 星アイテム / ブロック障害物（奥から手前へ流れる）
- 敵キャラ 2体（FUWARO / MURO）
- UI: LAP 1/3・★カウント・速度メーター・ミニマップ

#### 操作
- PC: ← → ↑ ↓ キー
- スマホ: 左右タッチゾーン・中央 ▲ ボタン

#### キャラクター描画システム
- 全キャラを Canvas API のみで図形描画（画像素材ゼロ）
- `drawChar(ctx, char, x, y, size)` — フロントビュー
- `drawCharBack(ctx, char, x, y, size)` — バックビュー（プレイ時）
- MIKARO 特有の耳・カラーは `char.id` で分岐

### 設計方針
- `index.html` 1ファイル完結（外部ライブラリなし）
- `assets/` に PNG を置けば差し替えられる構造（現状はフォールバック描画）
- スマホ縦画面 (390×844) をメインに CSS 最適化

### 課題・次フェーズへの引き継ぎ
- ゲームオーバー / ゴール / ラップカウントはまだ簡易 (Phase 2 で実装)
- 星の収集コリジョンは未実装 (Phase 2)
- ロックキャラの解放条件は未定義 (Phase 2)
- BGM / SE はなし (Phase 3 予定)
- 画像素材差し替え対応は Phase 2 以降で本格化

### コミット
`Add initial playable HTML prototype`

---

## Phase 3 — ビジュアルポリッシュ (2026-05-04)

### 目標
参考画像 3枚に合わせて全画面のビジュアルを大幅改善し、画像差し替え基盤も整備する。

### 実装内容

#### 共通
- CSS `-webkit-text-stroke` + `paint-order: stroke fill` で太い白アウトライン付きタイトルを実現
- `buildBunting()` でカラフルな三角旗ガーランドを動的生成
- `preloadImages()` + `IMG` キャッシュで画像プリロード基盤を追加
  - `assets/characters/*.png` / `assets/bg/*.png` が存在すれば優先表示
  - なければ Canvas フォールバックを使用（動作は変わらず）

#### スタート画面
- タイトル「ころころレース」を大型ストローク文字に変更（参考画像準拠）
- 「Ouchi Colon」白ピルバッジ
- キャラクター配置を参考画像の構図（後列→前列の3段配置）に修正
- MIKARO を中央最大表示に
- 木の床テクスチャ（横プランク線）を強化

#### キャラクター選択画面
- 各キャラに固有の Canvas 描画関数を追加：
  - MIKARO: 黄色ボディ・黒フェイス・白うさ耳・ピンク内耳
  - ピトコロ: 赤てんとう虫・黒スポット・アンテナ
  - マリンカバ: 青灰カバ・大きな目・鼻孔
  - フワロ: ピンク・花飾り
  - ムーロ: 濃緑ボックス・デジタルディスプレイ顔
  - カクバケ: TV スーツケース・青スクリーン
  - タコロン: 赤ドーム・金王冠・8本脚
  - ゾノ: 紫ロボット・緑LEDアイ

#### プレイ画面
- 「Ouchi Colon / ころころレース」タイトルを画面上部中央に常時表示（HTML オーバーレイ）
- 背景描画を全面刷新：
  - 三角旗ガーランド（上端）
  - 窓・青カーテン（左）、絵（壁）、ソファ（中央）、本棚・リングタワー（右）
  - 植物・時計・犬・積み木（アクセント）
  - より自然な木の床テクスチャ（縁石外側）
- コース縁石を太い赤白交互ストライプに改善
- 中央破線を黄色に変更
- ブロック障害物に3D感（トップ面・サイドシャドウ）を追加
- 星アイテムにグロー effect（shadowBlur）を追加

### 設計上の変更点
- `drawChar()` が画像優先 → Canvas フォールバックの2段構造に
- 各キャラ専用の `drawMikaro()` 等を独立関数として定義
- フォールバックのバックビュー `drawCharBack()` も id-based 描画

### コミット
`Add reference-based visual polish`

---

## Phase 4 — 実PNG素材パス統一と反映 (2026-05-04)

### 目標
Phase 3 で想定していた架空パスを、実際に用意された素材ファイルのパスに統一し、  
ゲーム全画面で PNG 素材が正しく表示されるようにする。

### 実ファイル構成（確定版）

| キー | 実ファイルパス | 用途 |
|---|---|---|
| `ui_title` | `assets/ui/title_logo.png` | タイトルロゴ |
| `bg_living` | `assets/backgrounds/living_room_bg.png` | スタート・プレイ背景 |
| `char_mikaro` | `assets/characters/mikaro_front.png` | MIKARO正面 |
| `char_mikaro_back` | `assets/characters/mikaro_back.png` | MIKARO背面（プレイ時） |
| `char_pitokoro` | `assets/characters/pitokoro_front.png` | ピトコロ |
| `char_marine` | `assets/characters/marinkaba_front.png` | マリンカバ |
| `char_fuwaro` | `assets/characters/fuwaro_front.png` | フワロ |
| `char_muro` | `assets/characters/moolo_front.png` | ムーロ / Moolo |
| `char_kakubake` | `assets/characters/kakubake_front.png` | カクバケ |
| `char_takoron` | `assets/characters/takoron_front.png` | タコロン |
| `char_zono` | `assets/characters/zono_front.png` | ゾノ |
| `item_star` | `assets/items/star.png` | 星アイテム |
| `item_block` | `assets/items/block_blue.png` | ブロック障害物 |

### 変更内容

#### 画像読み込み
- `IMG_PATHS` を上記パスに全面更新
- Phase 3 で使っていた架空パス（`assets/bg/start_bg.png` 等）を完全削除
- `preloadImages()` に読み込み完了時の再描画トリガーを追加
  - `ui_title` ロード → `#titleLogoImg` に src を設定、CSS フォールバックを非表示
  - `bg_living` / `char_*` ロード → アクティブ画面を再描画

#### スタート画面
- タイトルロゴ: `<img id="titleLogoImg">` を追加（ロード前は CSS テキストを表示）
- 背景: `bg_living` が読み込まれたら `living_room_bg.png` 全面描画 → キャラを上乗せ
- `living_room_bg.png` がない場合は従来の Canvas フォールバック描画を維持

#### キャラクター選択画面
- `drawChar()` が `'char_' + char.id` キーで自動的に PNG を使用
  - `char_marine` → `marinkaba_front.png`（ファイル名の差異を IMG_PATHS で吸収）
  - `char_muro` → `moolo_front.png`（同上）
- PNG なしの場合は従来の Canvas フォールバックを維持

#### プレイ画面
- 背景: `bg_living` の上部 58% をクロップして horizon ライン上に描画
- プレイヤー（MIKARO）: `mikaro_back.png` を優先 → ない場合は正面を反転フォールバック
- 敵キャラ: 両方 `moolo_front.png` を使用（charIdx: 4 / `char_muro`）
- 星アイテム: `star.png` を drawStar3D で優先使用
- ブロック: `block_blue.png` を drawBlock3D で優先使用
- 全て PNG なしでも Canvas フォールバックで動作維持

### コミット
`Fix PNG asset paths and rendering`
