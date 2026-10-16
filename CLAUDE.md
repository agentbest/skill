# skill — 手に職をつける求人サイト skill.agent-best.net

**「エージェントベストジョブ手に職」**。jobs.agent-best.net（agentbest/jobsite）から 2026-10-15 に分けた姉妹サイト。
2026-10-16 から **jobs は中途全件、skill はそのうち「未経験可」と「ブルーカラー・現場系の職種」だけ**を載せる（松岡さん指示。それまでは年収600万円未満を全部こちらに出していた）。

- 公開URL: https://skill.agent-best.net/ （GitHub Pages）
- リポジトリ: agentbest/skill（Public）
- 仕組み・データの持ち方・マイページ・静的ページなど**共通部分の説明は jobsite の CLAUDE.md を読む**（コードは jobsite の複製で、同じ構成）。
  このファイルには **skill だけの違い**を書く。

## ⚠ 振り分け（いちばん大事）

`site-split.js` が基準を持つ。**jobsite と skill でまったく同じ中身（鏡写し）。片方だけ直さない。**

| | 条件 |
|---|---|
| jobs | 中途の求人**すべて**（年収・職種で落とさない） |
| skill | jobs の一部。次のどれか ① 未経験可（タグ `UNEXPERIENCED_TAGS`＝未経験歓迎・業界未経験歓迎・職種未経験歓迎・未経験から年収400万円以上、または求人名・職種名に「未経験」） ② 職種の大分類が `BLUE_COLLAR_GROUPS`（施工管理・小売飲食・交通運輸・旅行ホテル・保育・医療看護薬剤）か小分類が `BLUE_COLLAR_CATEGORIES`（CAD・積算・在庫管理・生産管理・品質管理など） ③ 職種が「その他」「オープンポジション」・空で、求人名に `BLUE_COLLAR_WORDS`（製造・配送・倉庫・介護・清掃・整備・販売…） |

- 年収は見ない（旧基準 2026-10-15〜10-16 は「jobs＝年収下限600万円以上かつ施工管理以外、skill＝それ以外」で重複なしだった）。
- 機械設計・回路設計・生産技術・建築設計などの技術職（ホワイトカラー寄り）は、未経験可でなければ skill に出ない。入れたくなったら `BLUE_COLLAR_CATEGORIES` に足す。
- 2026-10-16 時点 skill 1,789件（未経験可 1,025・ブルーカラー系 1,102、重なりあり）／jobs 8,426件。
- `rebuild.js` の `SITE`（jobsite は `'jobs'`、ここは `'skill'`）で、どちら側を載せるかが決まる。
- skill 側の `data/moved.json` は jobs だけにある求人。`404.html` が `/job/<ID>/` で来た人を jobs の同じ求人へ転送する。jobs 側の moved.json は空。
- Airtable は触らない。掲載先はビルドだけで決める。
- 基準を変えたら**両方のリポジトリで rebuild して両方 push**。片方だけだと、その日は同じ求人が両方に出る／どちらにも出ない。

## jobsite との違い

- 名前・文言: `エージェントベストジョブ手に職`／title `手に職をつける転職の求人検索`。ヒーロー「一生もののスキルを、仕事で身につける。」
- テーマカラー: 藍 → **深緑**（`--accent:#0F6B5C`）。ヒーローのスクリムも緑寄り。
- ヒーローの人気の条件・件数: 年収800万／1,000万 の代わりに **未経験歓迎**（`inexperienced-ok`）／**資格取得支援あり**（`qualification-support`）。
  ボタンは `data-hq="tag:<slug>"`。件数の要素IDは jobsite と揃えて `count800`/`count1000` のまま（中身は TAG_COUNT）。
- 特集（`FEATURES`）: 研修ありで未経験から／資格取得支援／専門資格を活かせる／専門性を高められる／未経験からエンジニア／施工管理／機械・電気の技術職／第二新卒歓迎。
- 一覧ページの説明文・統計は「年収800万円以上」→「未経験歓迎」（`static-pages.js` の `nSal800` を流用）。
- **企業ページは作るが canonical は jobs 側**（`company-pages.js` の `CANON_SITE`）。sitemap にも入れない。同じ会社のページが2ドメインに並んで重複扱いされるのを避けるため。
- `SITE_KEY = "skill"`（マイページの `profiles.site`）、申し込みの `source: "skill"`。
- フッターに jobs への導線「ハイクラスを含むすべての中途求人を探す」。jobs 側にも skill への導線がある。
- meta description は「施工管理・製造・販売・介護などの現場の仕事から、未経験から始められる求人まで」（10/16。それまでは「エンジニア・機械・電気などの技術職から」）。

## 未対応（2026-10-15 時点）

- Supabase の Redirect URLs に `https://skill.agent-best.net/**` は 2026-10-15 に追加済み。
- 通知（Edge Function `notify-slack` の `SITES`、Apps Script、管理画面 jobsite-admin）は `source/site = "skill"` を知らない。今は中途扱いのラベルで届く。
- `assets/ogp.jpg` は skill 用（2026-10-15・原本 `tools/ogp/src.html` を Chrome ヘッドレスで 1200×630 に出力→JPG）。`assets/hero.*` は jobs と同じ写真。
- `data/first-seen.json` は jobs からの写しで始めた（掲載開始日を引き継ぐため）。

## 自動更新

jobsite と同じ `.github/workflows/update-jobs.yml`（毎朝06:00 JST）。Secret `AIRTABLE_TOKEN` が必要。
作業前に `git pull`。push のルールは jobsite の CLAUDE.md と同じ。
