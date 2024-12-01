---
title: "Jekyllでカテゴリorタグの一覧へのリンクを作る（苦肉の策）"
layout: post
category: プログラミング
tag:
- jekyll
---

### やりたいこと

特定のカテゴリへのリンクをクリックすると、そのカテゴリにひも付く記事一覧が表示される。

### 試したこと

- [jekyll-archives](https://github.com/jekyll/jekyll-archives) というプラグインがある
  - カテゴリやタグのページを生成してくれるが、Github pages でサポートされていない
- 調べた感じだとカテゴリ毎のページ、タグ毎のページを簡単に生成する方法がなさそう
  - Jekyllでは動的にページを生成するのが難しい？
- 苦肉の策として、カテゴリをすべて表示するページを作成してそこへフラグメント（#〜〜〜）を付与したURLでリンクする
  - カテゴリにひも付く記事も表示

例

カテゴリ一覧  
`categories.md`
{% raw %}
```html
{% for category in site.categories %}
  {% capture name %}{{ category | first }}{% endcapture %}
  <h2 id="{{ name }}">{{ name }}</h2>
  <ul>
  {% for post in site.categories[name] %}
    <li>
      <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
      <span>{{ post.date | date: site.minima.date_format }}</span>
    </li>
  {% endfor %}
  </ul>
{% endfor %}
```
{% endraw %}

カテゴリへのリンク  
`post.html`
{% raw %}
```html
<span>
  {% capture link_with_anchor %}{% link categories.md %}#{{ page.category }}{% endcapture %}
  <a href="{{ link_with_anchor | relative_url }}">{{ page.category }}</a>
</span>
```
{% endraw %}
