# kwmt27.net（Androg）

Hugo 製の個人ブログ。テーマは `themes/puppet`（git submodule）。`master` に push すると GitHub Actions が `hugo --minify` でビルドして gh-pages にデプロイする。

## コマンド

- ローカルプレビュー: `hugo server -D`（下書きも表示）→ http://localhost:1313/
- ビルド確認: `hugo --minify`（CI は Hugo 0.111.3。それより新しい構文は使わない）
- `public/` と `resources/` はビルド生成物。gitignore 済みなのでコミットしない

## 記事を書く・レビューする

- 記事ファイルの形式と配置: `.claude/rules/post-format.md`
- 文体ガイド（本人らしさの定義）: `.claude/rules/writing-style.md`
- どちらも `content/` 配下の Markdown を読むと自動で読み込まれる。新規記事を書くときは先に読むこと
- 新規記事の下書き: `/blog-write`
- 公開前レビュー: `/blog-review`

## 注意

- 記事内のターミナルログには本名のホスト名や AWS アカウント ID が混ざりやすい。公開前に `/blog-review` で機密チェックを通す
- 記事の front matter を他記事からコピーしたら、title / date / categories / tags / keywords を全部書き換える
- コードブロック内に `{{<` を書くと Hugo がショートコードとして解釈してビルドが落ちる。`{{</* ... */>}}` でエスケープする
