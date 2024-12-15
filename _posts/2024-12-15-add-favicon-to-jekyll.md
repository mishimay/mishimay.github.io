---
title: "Jekyllでfaviconをとりあえず表示させる"
layout: post
category: プログラミング
tag:
- jekyll
---

## 手順
1. `favicon.ico` を用意
1. プロジェクトフォルダ直下に置く

HTMLのheaderに `<link>` タグで指定しなくても `/favicon.ico` を見に行くらしい。
<https://html.spec.whatwg.org/multipage/links.html#rel-icon>

## Chromeでの確認方法
1. `~/Library/Application\ Support/Google/Chrome/Default/Favicons` を削除
1. ブラウザ再起動

## 環境
- github-pages
- jekyll (= 3.10.0)
- minima (= 2.5.1)
