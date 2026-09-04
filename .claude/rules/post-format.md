---
paths:
  - "content/**/*.md"
  - "archetypes/*.md"
---

# 記事ファイルの形式（Hugo）

## 配置と URL

- 記事は `content/post/YYYY/MM/<slug>.md`。`YYYY/MM` は front matter の `date` と一致させる
- URL は `/:year/:month/:day/:filename/`（config.toml の permalinks）。ファイル名がそのまま URL になるので、英小文字とハイフンの kebab-case にする
  - 例: `how-to-run-mcp-server-on-remote-server.md` → `/2025/04/19/how-to-run-mcp-server-on-remote-server/`
- 記事内で過去記事にリンクするときは `[2024年の目標](/2024/01/08/new-years-resolution/)` のように permalink 形式の絶対パス
- 画像は `static/images/YYYY/MM/<slug>/` に置き、`{{< figure src="/images/YYYY/MM/<slug>/name.png" >}}` で参照
- Zenn に書いた記事の転載は、Zenn の画像 URL（`https://storage.googleapis.com/zenn-user-upload/...`）を `![](...)` でそのまま使ってよい

## front matter（TOML）

```toml
+++
title = "記事タイトル"
date = "2026-09-04T12:00:00+09:00"
draft = false
toc = true
slug = ""
author = "kwmt27"
categories = ["Android"]
tags = ["Android", "Jetpack", "Compose"]
keywords = ["Android", "Jetpack", "Compose"]
+++
```

- `date` は JST（`+09:00`）。公開日時に合わせる。ディレクトリの `YYYY/MM` とずれていたら間違い
- `categories` は 1 つだけ。既存のものに合わせ、表記ゆれを増やさない
  - 既存: `Android`, `Flutter`, `iOS`, `Go`, `Kotlin`, `MCP`, `IntelliJ`, `Docker`, `macOS`, `日記`, `業務外活動`, `プライバシーポリシー`, `リリース情報`, `Android研究&発表会`
- `tags` と `keywords` は記事の内容に合わせて毎回書き直す
  - 他記事から front matter をコピーすると、Compose の keywords が Next.js の記事に残るような事故が起きる（実際に起きた）
- 書きかけは `draft = true`。`hugo server -D` で確認できる
- `slug = ""` と `author = "kwmt27"` は固定
- `hugo new post/YYYY/MM/<slug>.md` で `archetypes/post.md` からこの形の front matter が生成される（`draft = false` で作られるので下書き中は `true` に変える）

## 見出し

- 本文の大見出しは `#`（H1）。既存記事はすべてこの形
- 基本形: `# はじめに` → 本文（`#` 単位で区切る）→ `# おわりに`（または `# まとめ` / `# さいごに`）→ `# 参考`
- 小見出しは `##` `###`。`toc = true` で目次が出るので、見出しだけ追えば流れがわかるようにする
- 見出しは短く、話し言葉でよい（例: `# 動かしてみる`, `## 改めてリポジトリをクローンします`, `## SearchBarのUIを作るには？`）。英語見出し（`## Deploy to Cloudflare`）も可

## ショートコード・埋め込み

| 用途 | 書き方 |
| --- | --- |
| 画像 | `{{< figure src="/images/..." >}}`、幅指定は `width="300"` |
| GIF | `<img src="/images/..." />` の直書きでもよい |
| YouTube | `{{< youtube VIDEO_ID >}}` |
| Gist（旧記事のみ） | `{{< gist GIST_ID "File.kt" >}}`（`layouts/shortcodes/gist.html`） |
| GitHub 上のファイル（旧記事のみ） | `{{< gistit user/repo/blob/branch/path.kt "10:20" >}}`（gist-it.appspot.com 経由） |

- Gist と gistit は 2019〜2021 の記事で使っていたもの。2023 年以降はコードを本文のコードブロックに直接書いているので、新規記事でも Gist に置かず直接書く
- 試したコード全体を見せたいときは、リポジトリや PR の URL を貼る（例: `https://github.com/kwmt/camera-samples/pull/1/`）

## コードブロック

- 言語を必ず付ける: `shell`, `kotlin`, `ts`, `json`, `toml`, `yaml`, `diff`
  - つづり間違いに注意（過去に `kotiln` があった。ハイライトが効かなくなる）
- 変更前後を見せるときは `diff` か、行頭に `+` / `-` を付ける。本人はこの書き方をよく使う
- コードブロック内に `{{<` や `{{%` を書くと Hugo がショートコードとして解釈する。`{{</* ... */>}}` でエスケープ
- ターミナルの出力はそのまま貼ってよい。ただし次は伏せる
  - ローカルの絶対パス `/Users/kwmt/...` → `~/...` や `<path>`
  - プロンプトのホスト名 `[kwmt@MacBook-Pro]`
  - AWS アカウント ID（12 桁）、IAM ユーザー名、アクセスキーの一部（`****************UTMU` のような末尾 4 桁も）
  - Cloudflare の workers.dev サブドメイン、KV namespace id は `<name>`, `xxxxxxxx` に置き換える（本人の既存表記）
