# Ikea or Death: Requiem - 要件定義書

## 1. プロジェクト概要

**コンセプト**: 「IKEAの家具名か、デスメタルバンド名か？」を当てるクイズゲーム。2013年に200万ヒットを記録した伝説のネタサイト [ikeaordeath.com](http://ikeaordeath.com/) の精神的続編を、最新テクノロジーで構築する。

**コアバリュー**: IKEAのスカンジナビアン・ミニマルデザインとデスメタルのダークさが衝突する「ギャップの面白さ」

**リポジトリ**: https://github.com/GGSSKK/IkeaOrDeath-Requiem

---

## 2. ターゲットユーザー

- **メイン**: 一般ネットユーザー（SNS経由でバイラル拡散を狙う）
- **サブ**: メタルファン、IKEA好き・インテリア層
- **地域**: グローバル（英語 + 日本語対応）

---

## 3. 機能要件

### 3.1 クイズ機能（コア）

#### ゲームモード（3種）
| モード | ルール | スコア |
|--------|--------|--------|
| **Classic** | ランダム20問 | X/20 |
| **Endless** | 間違えたら終了 | 連続正解数 |
| **Time Attack** | 60秒以内に何問正解できるか | 正解数/60秒 |

#### 出題仕様
- データプールからランダムに出題
- IKEA商品名とメタルバンド名の出題比率は約50:50
- 同一セッション内で同じ問題は出さない
- 2択: 「IKEA」 or 「DEATH」

#### 正解後の表示
- 正解/不正解のフィードバック
- 名前 + 簡単な説明（例: 「KALLAXはIKEAのシェルフユニットです」「Opethはスウェーデンのプログレッシブデスメタルバンドです」）
- IKEA商品の場合: 商品ページへのアフィリエイトリンク
- バンドの場合: Spotify試聴リンク

### 3.2 サウンド演出
- **デスメタルバンド正解時**: メタルリフ / グロウルSE
- **IKEA正解時**: IKEA店内BGM風の穏やかなSE
- **不正解時**: 不正解SE
- **ミュートトグル**: デフォルトON、ワンタップでON/OFF切替

### 3.3 結果・シェア機能
- 結果画面にスコア表示
- **OGP画像動的生成**: スコア・モード・プレイヤー称号を含む画像をサーバーサイドで生成
- X(Twitter)、Facebook、LINEへのシェアボタン
- シェアテキスト例: "I scored 18/20 on Ikea or Death: Requiem! 🤘 Can you beat me?"

### 3.4 多言語対応（i18n）
- 英語（デフォルト）+ 日本語
- UI文言の切替
- クイズの問題データは共通（商品名・バンド名は言語非依存）
- 説明文のみ各言語で用意

### 3.5 マネタイズ
- **IKEAアフィリエイト**: 正解表示時にIKEA商品へのアフィリエイトリンク（Awin/CJ Affiliate経由）
- **Spotifyアフィリエイト**: バンド正解時にSpotify試聴リンク
- **広告**: Google AdSense（結果画面、ページ下部等）

---

## 4. データ仕様

### 4.1 IKEA商品データ
- **ソース**: Kaggle IKEA Furniture Dataset を初期データとして静的JSON保持
- **更新**: 定期的にスクレイピングスクリプトで更新
- **データ構造**:
```json
{
  "id": "ikea_001",
  "name": "KALLAX",
  "type": "ikea",
  "category": "Shelving unit",
  "description_en": "A versatile shelving unit",
  "description_ja": "多目的シェルフユニット",
  "product_url": "https://www.ikea.com/...",
  "image_url": "https://..."
}
```

### 4.2 メタルバンドデータ
- **ソース**: Metal Archives（非公式API / Hugging Faceデータセット）
- **試聴リンク**: Spotify Web API で取得
- **フィルタ**: デスメタル、ブラックメタル、スウェーデン/ノルウェー/フィンランドのバンドを優先
- **データ構造**:
```json
{
  "id": "metal_001",
  "name": "Opeth",
  "type": "metal",
  "genre": "Progressive Death Metal",
  "country": "Sweden",
  "description_en": "Swedish progressive death metal band formed in 1990",
  "description_ja": "1990年結成のスウェーデンのプログレッシブデスメタルバンド",
  "spotify_url": "https://open.spotify.com/artist/...",
  "metal_archives_url": "https://www.metal-archives.com/bands/..."
}
```

### 4.3 データ量目標
- IKEA商品名: 200+ エントリ
- メタルバンド名: 200+ エントリ
- 合計: 400+ エントリのプール

---

## 5. デザイン仕様

### 5.1 デザイントーン
**IKEA風ミニマルデザイン × メタルのダークさのギャップ**

- ベースはIKEAのスカンジナビアン・ミニマルデザイン（明るい、クリーン、青×黄）
- 回答時にメタル的な演出（画面が暗転、フォントがゴシック体に変化等）
- この「ギャップ」こそがこのサイトの面白さ

### 5.2 キーカラー
- IKEA側: `#0058A3`（IKEAブルー）、`#FFDA1A`（IKEAイエロー）
- METAL側: `#1A1A1A`（黒）、`#8B0000`（ダークレッド）、`#C0C0C0`（シルバー）

### 5.3 フォント
- IKEA側: Noto Sans / 清潔感のあるサンセリフ
- METAL側: ゴシック/ブラックレター系フォント（演出用）

### 5.4 アニメーション
- 問題表示時: フェードイン / スライドイン
- 正解時: IKEA → 明るいパルスエフェクト / METAL → 画面振動 + 暗転フラッシュ
- 不正解時: 画面シェイク
- モード選択: カード型のホバーエフェクト

---

## 6. 技術スタック

| 領域 | 技術 |
|------|------|
| **フレームワーク** | Next.js 15 (App Router) |
| **言語** | TypeScript |
| **スタイリング** | Tailwind CSS v4 |
| **アニメーション** | Framer Motion |
| **i18n** | next-intl |
| **OGP画像生成** | Vercel OG (@vercel/og / ImageResponse API) |
| **デプロイ** | Vercel |
| **データ** | 静的JSON（Git管理）+ Spotify API |
| **広告** | Google AdSense |
| **アフィリエイト** | IKEA (Awin/CJ) + Spotify |
| **アナリティクス** | Vercel Analytics / Google Analytics |
| **サウンド** | Howler.js or Web Audio API |

---

## 7. 画面構成

### 7.1 トップページ (`/`)
- ロゴ + タイトル「Ikea or Death: Requiem」
- サブタイトル「Is it Swedish furniture or a death metal band?」
- モード選択（Classic / Endless / Time Attack）
- 言語切替
- サウンドON/OFFトグル

### 7.2 クイズ画面 (`/play/[mode]`)
- 問題名 大きく表示
- 「IKEA」「DEATH」の2択ボタン
- プログレスバー（Classic: X/20、Endless: 連続正解数、Time Attack: 残り時間）
- スコア表示
- ミュートボタン

### 7.3 回答フィードバック（モーダル/オーバーレイ）
- 正解/不正解表示
- 名前 + 説明 + リンク
- 「次へ」ボタン

### 7.4 結果画面 (`/result/[session-id]`)
- 最終スコア
- 称号表示（例: "Furniture Noob" / "Metal Master" / "Scandinavian Scholar"）
- シェアボタン（X, Facebook, LINE）
- 「もう一度遊ぶ」ボタン
- 広告枠

### 7.5 OGP画像 (`/api/og`)
- スコア、モード、称号を含む動的画像
- 1200x630px
- IKEA × メタルのデザイン

---

## 8. 認証
- **なし**（アノニマス）
- スコアはCookie/localStorageで保持（オプション）

---

## 9. SEO対策
- 各ページにメタタグ
- 構造化データ (JSON-LD)
- サイトマップ自動生成
- OGP画像で検索・SNS最適化

---

## 10. 非機能要件

| 項目 | 要件 |
|------|------|
| **パフォーマンス** | Lighthouse 90+ (Performance) |
| **レスポンシブ** | モバイルファースト、PC/タブレット対応 |
| **アクセシビリティ** | WCAG 2.1 AA準拠（キーボード操作、スクリーンリーダー対応） |
| **ブラウザ** | Chrome, Safari, Firefox, Edge 最新版 |
| **可用性** | Vercel の SLA に準拠 |

---

## 11. 将来の拡張（Phase 2以降）
- リーダーボード / ランキング機能
- デイリーチャレンジ（毎日同じ問題セット）
- マルチプレイヤー対戦
- 追加ジャンル（ポケモン技名 vs 医薬品名 等）
- PWA対応
- ダークモード/ライトモード切替
