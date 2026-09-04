---
name: blog-write
description: kwmt27.net（Hugo）と Zenn の新規記事の下書きを本人の文体で書く。テーマ・メモ・ターミナルログ・スクショの説明から「調べたのでメモしておく」調の記事にし、front matter 付きで Hugo なら content/post/YYYY/MM/ に、Zenn なら zenn-content リポジトリの articles/ に draft として置く。「記事書いて」「これをブログにして」「下書き作って」「Zenn 用に書いて」のときに使う。
argument-hint: [テーマ or 元メモのパス] [--zenn]
allowed-tools: Read, Write, Edit, Glob, Grep, Bash(ls:*), Bash(mkdir:*), Bash(date:*), Bash(git status:*)
---

# blog-write

本人（kwmt）が書いたように読める下書きを作る。うまく書くことより、本人らしく書くことを優先する。

## 最初に読むもの

1. `.claude/rules/writing-style.md`（文体の正解。必ず読む）
2. `.claude/rules/post-format.md`（front matter・配置）
3. [examples.md](examples.md)（本人の文章の抜粋。語尾と間の取り方をここから真似る）
4. [template.md](template.md)（記事タイプ別の骨組み）

## 手順

### 1. 材料を集める

- `$ARGUMENTS` を見る。テーマだけ / メモファイルのパス / ターミナルログ / URL のどれか
- 出力先を決める。`--zenn` か「Zenn 用に」と言われたら Zenn（手順 5）。指定がなく技術記事なら、本人の方針（技術記事は Zenn、振り返り・目標・業務外活動・Zenn の転載はこのブログ）に沿って Zenn でよいか、下の質問と一緒に聞く
- 元メモがあれば全部読む。ログ・コード・エラー文は **一字も変えずに** 記事に貼る材料にする
- 足りない材料は一度にまとめて聞く。聞くのは次のうち本文に必要なものだけ
  - 何が気になって始めたか（はじめに の材料）
  - 何を見ながらやったか（公式ドキュメント、Zenn、Qiita の URL）
  - バージョン（Kotlin, AGP, Compose, Node, Hugo など）
  - どこで詰まって、どう直ったか。直っていないなら直っていないと書く
  - やってみた感想と、次にやりたいこと（おわりに の材料）
- 聞ける状況でなければ、仮の内容で埋めずに `<!-- TODO: ここに○○ -->` を本文に残して先に進む

### 2. 記事タイプを決める

| タイプ | 目安 | 語尾 | 骨組み |
| --- | --- | --- | --- |
| やってみた | 何かを動かした・作った。ログが多い | ですます | はじめに → 動かしてみる → 詰まった → 直った → おわりに → 参考 |
| 調査メモ | 疑問 1〜3 個を調べた。短い | 常体でよい | はじめに（疑問の箇条書き）→ 疑問ごとに `##` → 参考 |
| エラーメモ | エラー→原因→対策の集合 | 常体 | `## 対象` → `### エラー内容` / `### 原因` / `### 対策` / `### 該当箇所` |
| 解説 | ドキュメントを自分なりにまとめた | ですます | はじめに（参考元とバージョン）→ 概念ごとに `#` → 例 → 参考 |
| 一覧・チートシート | 移行メモ、置き換え表 | 体言止め | 参考 URL 1 行 → 箇条書きとコードブロック |
| 振り返り・目標 | 年末年始 | ですます、くだけてよい | はじめに → 前年の目標ごとに `>` 引用 + 結果 → 来年の目標 → さいごに |

迷ったら「やってみた」。

### 3. 書く

- template.md の骨組みをコピーして埋める。見出しは話し言葉で、記事の流れが見出しだけで追えるように
- 「はじめに」は動機から。目的の宣言、記事の構成説明は書かない
- 本文は時系列。コマンド → ログ → 一言、を繰り返す。ログの前後の説明は 1 行
- 詰まった箇所は「するとError...」→ ログ → 「コード見ると〜」→ 推測 → 「以下の方法で〜したほうがよさそうです」の流れで書く。失敗を隠さない
- ドキュメントの引用は `>` で原文（英語）、その下に `>` で日本語訳
- 確信がないことは本人の語尾（〜っぽい、〜ようです）でぼかしてよい。ただし **本人が確認していないことを AI が勝手に「〜っぽい」で書かない**。未確認は `<!-- TODO: 要確認 -->`
- 「おわりに」は感想 + 次にやること、2〜3 文。要約しない
- 「参考」は URL の箇条書きだけ
- 文の長さは気にしない。括弧の補足は長くていい
- writing-style.md の「本人が使わない表現」を 1 つも使わない

### 4. ファイルを置く

- 日付は今日（`date "+%Y-%m-%dT%H:%M:00+09:00"`）。本人から公開日の指定があればそれ
- パス: `content/post/YYYY/MM/<slug>.md`。slug は英小文字 kebab-case、内容が URL からわかる程度の長さ（`how-to-run-mcp-server-on-remote-server` くらい）
- `draft = true` で置く。公開判断は本人
- `categories` は既存から 1 つ。`tags` / `keywords` は本文から 2〜4 個
- 画像が必要なら `static/images/YYYY/MM/<slug>/` を作り、本文には `{{< figure src="/images/YYYY/MM/<slug>/<説明的な名前>.png" >}}` と `<!-- TODO: スクショを置く -->` を書く
- 既存ファイルを上書きしない。同名があれば聞く

### 5. Zenn 向け（`--zenn` または「Zenn 用に」と言われたとき）

- 出力先は Zenn のリポジトリ `/Users/kwmt/personal/blog/zenn/zenn-content/articles/<slug>.md`。`content/` には置かない。`$ARGUMENTS` で別の場所を指定されたらそちら
- slug は Hugo と同じ英小文字 kebab-case。ただし Zenn の制約で **英小文字・数字・ハイフン・アンダースコアの 12〜50 文字**。短い題材でも 12 文字は必要（`learn-nextjs` でちょうど 12）。ファイル名がそのまま `https://zenn.dev/yasi/articles/<slug>` になる
- 書く前に `ls /Users/kwmt/personal/blog/zenn/zenn-content/articles/` で同名がないか確認する。あれば聞く
- front matter は template.md の Zenn 用（YAML）。`published: false`。`emoji` は内容に合う絵文字 1 文字、`topics` は本文から 2〜4 個で既存記事の表記に合わせる（`Android`, `Kotlin`, `JetpackCompose`, `MCP`, `TypeScript`, `nextjs`, `gradle` など）
- 本文の書き方は Hugo 向けと同じ。違いは次だけ
  - 画像は Zenn にアップロードする前提で `![](<!-- TODO: zenn-user-upload の URL -->)`
  - Hugo のショートコードは使わない（`{{< figure >}}` → `![]()`、`{{< youtube >}}` → URL 直貼り。Zenn は URL を 1 行で置くとカードになる）
  - ファイル名付きコードブロック（` ```kotlin:build.gradle.kts `）と `:::message` 〜 `:::` は本人が Zenn で使っているので使ってよい
  - 過去記事へのリンクは `/YYYY/MM/DD/slug/` ではなく `https://zenn.dev/yasi/articles/<slug>`
- 文体・形式の正解はこのリポジトリの `.claude/rules/` にしかない（zenn-content には `.claude/` がない）。「最初に読むもの」を飛ばさない

### 6. 報告

- 作ったファイルのパス
- 本文に残した `TODO` の一覧（行番号付き）
- 確認方法: Hugo なら `hugo server -D` → `http://localhost:1313/YYYY/MM/DD/<slug>/`。Zenn なら zenn-content で `npx zenn preview` → `http://localhost:8000/articles/<slug>`
- 次は `/blog-review` で公開前チェック

## やらないこと

- ログや出力を「きれいに」整形しない。本人はそのまま貼る
- 本人が試していない手順・結果を書かない。試した結果が不明なら書かずに TODO
- 一般論の解説を足して記事を厚くしない。本人のメモが 5 行なら記事も短くていい（Chakra 移行メモは本文 30 行）
- 記事の最後に「まとめ」で本文を要約しない
- `draft = false` にしない
