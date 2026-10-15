# skill — 手に職をつける求人サイト skill.agent-best.net

**「エージェントベストジョブ手に職」**。jobs.agent-best.net（agentbest/jobsite）から 2026-10-15 に分けた姉妹サイト。
jobs を年収600万円以上のハイクラスに絞ったので、**それ以外の中途求人はすべてこちらに出る**（松岡さん決定）。

- 公開URL: https://skill.agent-best.net/ （GitHub Pages）
- リポジトリ: agentbest/skill（Public）
- 仕組み・データの持ち方・マイページ・静的ページなど**共通部分の説明は jobsite の CLAUDE.md を読む**（コードは jobsite の複製で、同じ構成）。
  このファイルには **skill だけの違い**を書く。

## ⚠ 振り分け（いちばん大事）

`site-split.js` が基準を持つ。**jobsite と skill でまったく同じ中身（鏡写し）。片方だけ直さない。**

| | 条件 |
|---|---|
| jobs | 年収の**下限**が600万円以上（下限が無ければ上限）**かつ** 職種の大分類が施工管理でない |
| skill | それ以外すべて（600万円未満・年収未記載・施工管理・工場や店舗などの低年収も含む） |

- `rebuild.js` の `SITE`（jobsite は `'jobs'`、ここは `'skill'`）で、どちら側を載せるかが決まる。
- jobs の旧 `hiClassOnly()`（低年収の工場・店舗）と `noExcludedGroups()`（施工管理）は振り分けに吸収した＝**skill にはそれらも出る**（松岡さんの選択）。
- 相手のサイトに出る求人のIDは `data/moved.json`。`404.html` が `/job/<ID>/` で来た人を相手のサイトの同じ求人へ転送する（`OTHER_ORIGIN` は jobs と skill で逆）。
  年収が書き換わって求人がサイトをまたいでも、古いURLから辿れる。
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
- フッターに jobs への導線「年収600万円以上のハイクラス求人を探す」。jobs 側にも skill への導線がある。

## 未対応（2026-10-15 時点）

- Supabase の Redirect URLs に `https://skill.agent-best.net/**` は 2026-10-15 に追加済み。
- 通知（Edge Function `notify-slack` の `SITES`、Apps Script、管理画面 jobsite-admin）は `source/site = "skill"` を知らない。今は中途扱いのラベルで届く。
- `assets/ogp.jpg` は skill 用（2026-10-15・原本 `tools/ogp/src.html` を Chrome ヘッドレスで 1200×630 に出力→JPG）。`assets/hero.*` は jobs と同じ写真。
- `data/first-seen.json` は jobs からの写しで始めた（掲載開始日を引き継ぐため）。

## 自動更新

jobsite と同じ `.github/workflows/update-jobs.yml`（毎朝06:00 JST）。Secret `AIRTABLE_TOKEN` が必要。
作業前に `git pull`。push のルールは jobsite の CLAUDE.md と同じ。
