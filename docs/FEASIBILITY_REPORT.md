# 実現可能性調査レポート

> 調査日: 2026-02-18

## 調査サマリー

要件定義v1に対して3つの観点から徹底調査を実施。4件のSHOWSTOPPERと4件のHIGH RISKを発見し、要件定義v2で対策済み。

---

## SHOWSTOPPER（修正済み）

### 1. IKEAアフィリエイトが主要市場に存在しない
- **問題**: 米国・英国・カナダ・日本にIKEAアフィリエイトプログラムなし
- **対象地域**: スペイン、ドイツ/DACH(Tradedoubler)、オランダ(Awin)、インドのみ
- **対策**: → Amazon Associates に切替（US/JP/UK対応）

### 2. Spotify APIがクイズ/ゲームでの使用を明示的に禁止
- **Developer Policy**: "you may not use the Spotify Platform to incorporate Spotify Content into any game functionality (including trivia quizzes)"
- **Widget Terms**: 埋め込みプレーヤーも商用クイズ利用不可
- **対策**: → Bandcamp埋め込み（無料、制限なし）+ YouTube + Apple Music アフィリエイト

### 3. Spotify API 2026年2月の大幅制限強化
- Dev Mode: Premium必須、5ユーザー制限、検索結果10件上限
- Extended Access: 250K+ MAUのビジネスのみ
- **対策**: → Spotify API自体を使用しない

### 4. Vercel Hobbyプランは商用利用禁止
- Fair Use Guidelines: "Hobby teams are restricted to non-commercial personal use only"
- AdSenseを含むサイトは商用扱い
- **対策**: → Vercel Pro ($20/月) を初日から使用

---

## HIGH RISK（対策済み）

### 5. ドメイン名にIKEAを使うリスク
- IKEAは商標を積極的に守る（"The Store is Closed"にC&D送付実績）
- UDRP/ACPAリスクあり
- ただしikeaordeath.comは12年以上運用中
- **決定**: リスクを受容。代替ドメイン候補を事前準備

### 6. 紛らわしい名前が200+集まるか
- 本当に混同しやすい名前は推定150-250
- 質を保てるのは各120件程度
- **決定**: 120-150件に厳選。定期更新で補充

### 7. AdSense審査リスク
- クイズサイトは「低品質コンテンツ」で却下されやすい
- **対策**: 10ページ以上のテキストコンテンツを用意

### 8. OGP画像の日本語フォント
- Noto Sans JP: 5-16MB → Edge Function制限超過
- **対策**: サブセット化(200-500KB) + Node.js Runtime使用

---

## データソース

### IKEA商品データ
| ソース | 件数 | 更新日 | 備考 |
|--------|------|--------|------|
| Kaggle crawlfeeds US | ~27,000行 | 2021年6月 | CC0、US市場、スウェーデン語名確認済み |
| Kaggle ahmedkallam SA | 2,962行 | 2020年4月 | 家具のみ、SA市場 |
| Kaggle thedevastator | 3,694行 | 2022年11月 | 14カラム |
| IKEA Dictionary | ~1,362名 | 継続更新 | 名前+意味、URLなし |

**推奨**: crawlfeeds US dataset をメインに使用

### メタルバンドデータ
| ソース | 件数 | 更新日 | 備考 |
|--------|------|--------|------|
| Kaggle Every Metal Archives Band | 194,196バンド | 2024年11月 | 国・ジャンルフィルタ可能 |
| Hugging Face Jarbas/metal-archives-bands | 4,595行 | 不明 | 小規模サブセット |

**推奨**: Kaggle 194K dataset をメインに使用（スクレイピング不要）

### 音楽リンク
| サービス | 方式 | 制限 | 収益化 |
|----------|------|------|--------|
| Bandcamp | iframe埋め込み | なし | なし（アーティスト支援） |
| YouTube | iframe埋め込み | なし | なし |
| Apple Music | API + リンク | 要デベロッパーアカウント | 3-7%（Performance Partners） |
| Spotify | **使用禁止** | クイズ利用禁止 | N/A |

---

## 技術調査結果

### Vercel Pro コスト
- 2MPVまで$20-35/月で収まる
- デフォルト支出上限$200/月

### @vercel/og
- CSS gradient非対応 → 背景画像で対応
- 日本語フォント → サブセット化必須
- Node.js Runtimeなら250MB制限（Edge Runtimeの1-2MBに比べ余裕）

### Framer Motion
- LazyMotion + m コンポーネントで初期バンドル4.6KB
- 全アニメーション（シェイク、暗転、フェード等）実現可能
- React Server Components とは `'use client'` パターンで共存

### next-intl v4
- App Router完全対応、安定版
- `[locale]` セグメントルーティング（/en/play, /ja/play）
- SSG対応（setRequestLocale必須）

### Howler.js
- 7KB、オーディオスプライト対応
- ブラウザ自動再生制限はユーザークリック後なので問題なし

### AdSense + Next.js
- `'use client'` + dynamic import (ssr: false) で対応
- ads.txt を /public に配置
