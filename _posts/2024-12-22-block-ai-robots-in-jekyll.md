---
title: "AI学習を拒否するrobots.txtをJekyllに置く"
layout: post
category: プログラミング
tag:
- jekyll
---

## 手順

1. `_config.yml` の `url:` にサイトのURLを記入
1. プロジェクトのルートに `robots.txt` を配置して以下を記入

{% raw %}
```
---
layout: null
sitemap: false
---

User-agent: GPTBot
Disallow: /
User-agent: ChatGPT-User
Disallow: /
User-agent: Google-Extended
Disallow: /
User-agent: PerplexityBot
Disallow: /
User-agent: Amazonbot
Disallow: /
User-agent: ClaudeBot
Disallow: /
User-agent: Omgilibot
Disallow: /
User-Agent: FacebookBot
Disallow: /
User-Agent: Applebot
Disallow: /
User-agent: anthropic-ai
Disallow: /
User-agent: Bytespider
Disallow: /
User-agent: Claude-Web
Disallow: /
User-agent: Diffbot
Disallow: /
User-agent: ImagesiftBot
Disallow: /
User-agent: Omgilibot
Disallow: /
User-agent: Omgili
Disallow: /
User-agent: YouBot
Disallow: /

Sitemap: {{ "sitemap.xml" | absolute_url }}
```
{% endraw %}


Sitemapは最後に書いたほうがいいらしい


## 参考

- <https://www.cyberciti.biz/web-developer/block-openai-bard-bing-ai-crawler-bots-using-robots-txt-file/>
- <https://nithinbekal.com/posts/jekyll-ai-scraping/>
- <https://github.com/jekyll/jekyll-sitemap/blob/master/lib/robots.txt>
