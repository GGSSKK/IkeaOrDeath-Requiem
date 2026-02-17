# Ikea or Death: Requiem - 要件定義書 v2

> **v2 更新**: 実現可能性調査の結果を反映。IKEA アフィリエイト不在、Spotify API クイズ利用禁止、Vercel 商用利用制限等の問題を解決済み。

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
| **Classic** | ランダム20問（難易度ミックス） | X/20 |
| **Endless** | 間違えたら終了（正解するほど難易度上昇） | 連続正解数 |
| **Time Attack** | 60秒以内に何問正解できるか | 正解数/60秒 |

#### 難易度システム
全データに「紛らわしさスコア」(confusion_score: 1-10) を付与し、難易度をグラデーション管理する。

| 難易度 | confusion_score | 例 | 特徴 |
|--------|----------------|-----|------|
| **Easy (1-3)** | 低 | BILLY(IKEA), CANNIBAL CORPSE(Metal) | 誰でもわかる。英語名、明らかに一方 |
| **Medium (4-6)** | 中 | HEMNES(IKEA), ENTOMBED(Metal) | 少し迷う。北欧風だが特徴あり |
| **Hard (7-8)** | 高 | MALM(IKEA), OPETH(Metal) | かなり紛らわしい。1語の北欧風 |
| **Impossible (9-10)** | 最高 | GRUNDTAL(IKEA), BATHORY(Metal) | ほぼ区別不能 |

- **Classicモード**: Easy 5問 + Medium 8問 + Hard 5問 + Impossible 2問 = 20問
- **Endlessモード**: 序盤はEasy→正解するほど徐々にHard/Impossibleの比率が上がる
- **Time Attackモード**: 全難易度ランダムミックス

#### 出題仕様
- 難易度に応じたプールからランダムに出題
- IKEA商品名とメタルバンド名の出題比率は約50:50
- 同一セッション内で同じ問題は出さない
- 2択: 「IKEA」 or 「DEATH」

#### 正解後の表示
- 正解/不正解のフィードバック
- 名前 + 簡単な説明（例: 「KALLAXはIKEAのシェルフユニットです」「Opethはスウェーデンのプログレッシブデスメタルバンドです」）
- IKEA商品の場合: Amazon商品ページへのアフィリエイトリンク（Amazon Associates）
- バンドの場合: Bandcamp埋め込みプレーヤー / YouTube埋め込み / Apple Musicリンク

### 3.2 サウンド演出
- **デスメタルバンド正解時**: メタルリフ / グロウルSE
- **IKEA正解時**: IKEA店内BGM風の穏やかなSE
- **不正解時**: 不正解SE
- **ミュートトグル**: デフォルトON、ワンタップでON/OFF切替
- **音源**: Pixabay / Freesound.org 等のロイヤリティフリー音源（CC0 or CC-BY）
- **実装**: Howler.js（7KB、オーディオスプライト対応）
- **ファイル形式**: MP3 128kbps（主）+ OGG（フォールバック）、合計200KB以内

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

> **調査結果**: IKEAアフィリエイトは米国・英国・日本に存在しない。Spotify APIはクイズ利用を明示的に禁止。戦略を修正済み。

| 収益源 | 方法 | 対象地域 |
|--------|------|----------|
| **Amazon Associates** | IKEA商品正解時にAmazon商品リンク | US, JP, UK等 |
| **Apple Music アフィリエイト** | バンド正解時にApple Musicリンク（Apple Services Performance Partners 3-7%） | グローバル |
| **Google AdSense** | 結果画面・ページ下部の広告枠 | グローバル |

**AdSense承認対策**: クイズ機能だけでは「低品質コンテンツ」で却下されるリスクあり。以下のテキストコンテンツページを用意:
- About / このサイトについて
- How It Works / 遊び方
- IKEA命名規則の豆知識ページ
- デスメタルバンド名の由来ページ
- FAQ
- Privacy Policy / Terms of Service
- Contact
→ **計10ページ以上**のテキストコンテンツを確保

### 3.6 法的対応

| リスク | 対策 |
|--------|------|
| **IKEA商標** | ドメインに"IKEA"を使用（リスク受容）。全ページにディスクレーマー表示: "This site is not affiliated with, endorsed by, or sponsored by Inter IKEA Systems B.V. IKEA is a registered trademark of Inter IKEA Systems B.V." |
| **バンド名使用** | ノミナティブ・フェアユース（情報提供目的の参照使用） |
| **IKEA商品画像** | **使用しない**。テキスト（商品名）のみ表示。画像著作権リスクを回避 |
| **音源** | ロイヤリティフリー（CC0/CC-BY）の音源のみ使用。CC-BYの場合はクレジットページに帰属表示 |

---

## 4. データ仕様

### 4.1 IKEA商品データ
- **一次ソース**: Kaggle crawlfeeds US dataset（27,000行、CC0ライセンス）からユニークなスウェーデン語商品名を**全件**抽出
- **補助ソース**: IKEA Dictionary (lar5.com/ikea) - 1,362件の商品名+意味
- **商品URL**: ikea.com の商品ページURL（参考リンク）+ Amazon Associatesリンク
- **更新**: 定期的にスクレイピングスクリプトで更新し、新商品を自動追加
- **confusion_score**: 各商品名に紛らわしさスコア(1-10)を付与。アルゴリズム+手動で判定
  - 名前の音韻的特徴（北欧語の暗さ、硬さ、長さ）
  - 英語話者にとっての不透明さ
- **データ構造**:
```json
{
  "id": "ikea_001",
  "name": "KALLAX",
  "type": "ikea",
  "category": "Shelving unit",
  "confusion_score": 8,
  "description_en": "A versatile shelving unit",
  "description_ja": "多目的シェルフユニット",
  "ikea_url": "https://www.ikea.com/us/en/p/...",
  "amazon_url": "https://www.amazon.com/dp/...?tag=xxx"
}
```
> **注意**: IKEA商品画像は著作権リスクのため使用しない

### 4.2 メタルバンドデータ
- **一次ソース**: Kaggle "Every Metal Archives Band" dataset（194,196バンド、2024年11月版）
- **フィルタ戦略（難易度別）**:
  - **Easy (1-3)**: 英語の長いバンド名（Cannibal Corpse, Morbid Angel等）→ 明らかにメタル
  - **Medium (4-6)**: 1-2語の英語バンド名（Entombed, Unleashed等）
  - **Hard (7-8)**: スカンジナビア系1語（Opeth, Meshuggah等）
  - **Impossible (9-10)**: スウェーデン語/古ノルド語由来の1語（Bathory, Vomitory等）→ IKEAと区別困難
- **対象ジャンル**: Death Metal, Black Metal, Doom Metal, Viking Metal等（全メタルサブジャンル）
- **対象国**: 全世界（スカンジナビアに限定しない。名前の音韻で判定）
- **音楽リンク**: Bandcamp埋め込み（主）+ YouTube埋め込み + Apple Music（アフィリエイト）
- **データ構造**:
```json
{
  "id": "metal_001",
  "name": "Opeth",
  "type": "metal",
  "genre": "Progressive Death Metal",
  "country": "Sweden",
  "confusion_score": 8,
  "description_en": "Swedish progressive death metal band formed in 1990",
  "description_ja": "1990年結成のスウェーデンのプログレッシブデスメタルバンド",
  "bandcamp_url": "https://opeth.bandcamp.com/",
  "youtube_url": "https://youtube.com/...",
  "apple_music_url": "https://music.apple.com/...",
  "metal_archives_url": "https://www.metal-archives.com/bands/Opeth/..."
}
```

> **Spotify について**: Spotify Developer Policy がクイズ/ゲームでのAPI・埋め込み使用を明示的に禁止しているため、Spotify APIは使用しない。外部リンク（`open.spotify.com` へのテキストリンク）のみ可能だが、優先度は低い。

### 4.3 データ量目標

| カテゴリ | Easy | Medium | Hard | Impossible | 合計 |
|----------|------|--------|------|------------|------|
| **IKEA商品名** | ~300 | ~400 | ~200 | ~100 | **~1,000+** |
| **メタルバンド名** | ~300 | ~400 | ~200 | ~100 | **~1,000+** |
| **合計** | 600 | 800 | 400 | 200 | **~2,000+** |

- IKEA: 1,362件のユニーク商品名から重複・無効を除去 → ~1,000件使用
- メタル: 194Kバンドからメタルジャンルで絞り込み → 数千〜数万件の候補から ~1,000件を選定
- **confusion_score の自動算出**: 名前の音韻特徴（母音/子音比率、北欧語音素、ウムラウト有無、語長）からスコアを機械的に算出し、境界値のみ手動調整

### 4.4 confusion_score 算出ロジック（案）
紛らわしさを定量化するためのヒューリスティクス:
- **北欧語音素の含有率**: å, ä, ö, ü 等の特殊文字 → スコア+2
- **語長**: 4-7文字の1語 → スコア+2（IKEAっぽくもメタルっぽくもある）
- **子音クラスタ**: kv, fj, str, gr 等の北欧的子音連結 → スコア+1
- **英語的明瞭さ**: 英語辞書に載っている単語 → スコア-3（明らかにメタル側）
- **ダークな意味**: death, blood, dark等の含有 → スコア-3（明らかにメタル側）
- **複数語**: 2語以上 → スコア-2（IKEAは基本1語）
- 最終スコアを1-10にクランプ

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
- METAL側: ゴシック/ブラックレター系フォント（演出用、軽量なTTF/OTF 50-200KB以内）
- 日本語: Noto Sans JP（サブセット化して使用）

### 5.4 アニメーション
- 問題表示時: フェードイン / スライドイン
- 正解時: IKEA → 明るいパルスエフェクト / METAL → 画面振動 + 暗転フラッシュ
- 不正解時: 画面シェイク
- モード選択: カード型のホバーエフェクト
- **実装**: Framer Motion の `LazyMotion` + `m` コンポーネント（初期バンドル ~4.6KB）

---

## 6. 技術スタック

| 領域 | 技術 | 備考 |
|------|------|------|
| **フレームワーク** | Next.js 15 (App Router) | |
| **言語** | TypeScript | |
| **スタイリング** | Tailwind CSS v4 | |
| **アニメーション** | Framer Motion (LazyMotion) | 初期バンドル ~4.6KB |
| **i18n** | next-intl v4 | `[locale]` セグメントルーティング |
| **OGP画像生成** | @vercel/og (ImageResponse API) | Node.js Runtime使用（Edge Runtime の 1-2MB制限回避） |
| **デプロイ** | Vercel **Pro** ($20/月) | **Hobby は商用利用禁止** |
| **データ** | 静的JSON（Git管理） | Metal Archives + IKEA データセット |
| **広告** | Google AdSense | クライアントコンポーネントとして実装 |
| **アフィリエイト** | Amazon Associates + Apple Music (Performance Partners) | |
| **アナリティクス** | Vercel Analytics / Google Analytics | |
| **サウンド** | Howler.js | 7KB、オーディオスプライト対応 |

### 6.1 技術的注意点

| 項目 | 対応 |
|------|------|
| **OGP日本語テキスト** | Noto Sans JP をサブセット化（pyftsubset等で200-500KB）してOGルートに使用 |
| **OGP背景** | @vercel/og は CSS gradient 非対応。事前レンダリング済み背景画像を使用 |
| **Framer Motion + RSC** | アニメーションコンポーネントは全て `'use client'` ディレクティブ付き |
| **AdSense + SSR** | AdSense コンポーネントは `'use client'` + `dynamic import (ssr: false)` |
| **next-intl + SSG** | 全 Server Component layout/page で `setRequestLocale(locale)` を呼び出し |

---

## 7. 画面構成

### 7.1 トップページ (`/[locale]`)
- ロゴ + タイトル「Ikea or Death: Requiem」
- サブタイトル「Is it Swedish furniture or a death metal band?」
- モード選択（Classic / Endless / Time Attack）
- 言語切替
- サウンドON/OFFトグル
- **ディスクレーマー**: IKEA非公式サイト表示

### 7.2 クイズ画面 (`/[locale]/play/[mode]`)
- 問題名 大きく表示
- 「IKEA」「DEATH」の2択ボタン
- プログレスバー（Classic: X/20、Endless: 連続正解数、Time Attack: 残り時間）
- スコア表示
- ミュートボタン

### 7.3 回答フィードバック（モーダル/オーバーレイ）
- 正解/不正解表示
- 名前 + 説明
- IKEA → Amazonリンク / バンド → Bandcamp埋め込み or YouTubeリンク
- 「次へ」ボタン

### 7.4 結果画面 (`/[locale]/result/[session-id]`)
- 最終スコア
- 称号表示（例: "Furniture Noob" / "Metal Master" / "Scandinavian Scholar"）
- シェアボタン（X, Facebook, LINE）
- 「もう一度遊ぶ」ボタン
- 広告枠

### 7.5 OGP画像 (`/api/og`)
- スコア、モード、称号を含む動的画像
- 1200x630px
- IKEA × メタルのデザイン（背景画像は事前レンダリング）
- Node.js Runtime で実行（Edge の 1-2MB バンドル制限回避のため）

### 7.6 コンテンツページ（AdSense承認用 + SEO）
- `/[locale]/about` - サイト紹介
- `/[locale]/how-to-play` - 遊び方
- `/[locale]/ikea-naming` - IKEA命名規則の豆知識
- `/[locale]/metal-naming` - デスメタルバンド名の由来
- `/[locale]/faq` - よくある質問
- `/[locale]/privacy` - プライバシーポリシー
- `/[locale]/terms` - 利用規約
- `/[locale]/contact` - お問い合わせ
- `/[locale]/credits` - クレジット・帰属表示

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
- `ads.txt` を `/public` に配置（AdSense用）

---

## 10. 非機能要件

| 項目 | 要件 |
|------|------|
| **パフォーマンス** | Lighthouse 90+ (Performance) - LazyMotion(4.6KB) + Howler.js(7KB) で達成可能 |
| **レスポンシブ** | モバイルファースト、PC/タブレット対応 |
| **アクセシビリティ** | WCAG 2.1 AA準拠（キーボード操作、スクリーンリーダー対応） |
| **ブラウザ** | Chrome, Safari, Firefox, Edge 最新版 |
| **可用性** | Vercel Pro の SLA に準拠 |

### 10.1 コスト見積もり

| 月間PV | Vercel Pro 月額 | 備考 |
|--------|----------------|------|
| ~100,000 | $20 | 基本料金内 |
| ~500,000 | $20 | 基本料金内 |
| ~1,000,000 | $20 | 基本料金内 |
| ~2,000,000 | $20-35 | 帯域わずかに超過 |
| ~5,000,000 | ~$245 | 帯域超過分 $0.15/GB |

※ デフォルト支出上限 $200/月（設定変更可）

---

## 11. 既知のリスクと対策

| リスク | 深刻度 | 対策 | 状態 |
|--------|--------|------|------|
| IKEAドメイン商標リスク | 高 | リスク受容 + ディスクレーマー表示。C&D受領時の代替ドメイン候補を事前準備 | 受容 |
| AdSense審査却下 | 高 | 10ページ以上のテキストコンテンツを用意 | 対策済み |
| confusion_scoreの精度 | 中 | 自動算出+手動調整のハイブリッド。ユーザーフィードバックで継続改善 | 対策済み |
| OGP日本語フォント | 中 | Noto Sans JP サブセット化 + Node.js Runtime | 対策済み |
| IKEA深層リンク禁止 | 低 | Amazonリンクを主軸に。IKEA直リンクは参考程度 | 対策済み |

---

## 12. 将来の拡張（Phase 2以降）
- リーダーボード / ランキング機能
- デイリーチャレンジ（毎日同じ問題セット）
- マルチプレイヤー対戦
- 追加ジャンル（ポケモン技名 vs 医薬品名 等）
- PWA対応
- ダークモード/ライトモード切替
- 独自ドメイン取得後のIKEA地域別アフィリエイト（DE/NL等対応地域向け）
