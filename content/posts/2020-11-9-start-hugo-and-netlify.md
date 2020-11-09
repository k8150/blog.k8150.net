---
author: "koji haigo"
title: "HUGO + Netlifyで静的サイトを生成する"
date: "2020-11-09"
description: ""
tags: ["HUGO", "Netlify"]
---

## HUGO

Go言語で実装された静的サイトジェネレータ。テーマが豊富。環境構築が楽。
今回はテーマを[hugo-PaperMod](https://github.com/adityatelange/hugo-PaperMod)で試した。

```sh
# Install Hugo
$ brew install hugo
# Create a New Site
$ hugo new site [任意のディレクトリ名]
# Add a Theme
$ git submodule add https://github.com/adityatelange/hugo-PaperMod.git themes/hugo-PaperMod
# add the theme to the site configuration:
$ echo 'theme: "hugo-PaperMod"' >> config.yaml
# Netlifyに上げた時、設定したドメイン名を指定すればよいのだが複数CNAME指定があるとCORSが発生してリソースを読み込めなくなるためbaseUrl: "/"を指定する。
# これが正しい解決策なのかは不明。
$ echo 'baseUrl: "/"' >> config.yaml
```

以上で準備完了。

```sh
# ローカルサーバ起動(localhost:1313/)
$ hugo server -D
```

## Netlify

Netlifyのホスト方法は公式ページを参照する。[Host on Netlify](https://gohugo.io/hosting-and-deployment/hosting-on-netlify/)

```sh
# netlify.tomlに公式手順に記載されたものをコピペする。
$ vi netlify.toml
```

GitHubの任意ブランチのプッシュをトリガーにNetlifyでデプロイが走る。
デプロイで下記のエラーが発生し解消できなかったが、`posts/`配下の資材を全削除して再生成したところ成功した。謎。
エラー詳細が出力されないためデプロイエラーの原因究明と解消に時間が掛かった。

Netlify デプロイエラー
```
9:46:19 PM: ┌─────────────────────────────┐
9:46:19 PM: │   "build.command" failed    │
9:46:19 PM: └─────────────────────────────┘
9:46:19 PM: ​
9:46:19 PM:   Error message
9:46:19 PM:   Command failed with exit code 255: hugo --gc --minify
9:46:19 PM: ​
9:46:19 PM:   Error location
9:46:19 PM:   In build.command from netlify.toml:
9:46:19 PM:   hugo --gc --minify
9:46:19 PM: ​
9:46:19 PM:   Resolved config
9:46:19 PM:   build:
9:46:19 PM:     command: hugo --gc --minify
9:46:19 PM:     commandOrigin: config
9:46:19 PM:     environment:
9:46:19 PM:       - HUGO_VERSION
9:46:19 PM:       - HUGO_ENV
9:46:19 PM:       - HUGO_ENABLEGITINFO
9:46:19 PM:     publish: /opt/build/repo/public
```

### ドメイン名設定

AWS Route53で取得したドメインにCNAMEレコードを指定するだけ。
ルートの場合、AレコードにIPを指定する。

### SSL化

ドメインを設定するとNetlifyの管理画面で自動でSSL化してくれる。便利〜😽😽😽

## 参考

- [静的サイトジェネレータ「Hugo」と技術文書公開向けテーマ「Docsy」でOSSサイトを作る](https://knowledge.sakura.ad.jp/22908/)
- [HUGO - Quick Start](https://gohugo.io/getting-started/quick-start/)
- [Host on Netlify](https://gohugo.io/hosting-and-deployment/hosting-on-netlify/)
- [CORS のエラー](https://developer.mozilla.org/ja/docs/Web/HTTP/CORS/Errors)