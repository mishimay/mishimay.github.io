---
title: "QiitaからJekyllへの移行"
layout: post
category: プログラミング
---

## Qiitaの記事を取得

<https://blog.kotet.jp/2017/01/64fd38ef5e10bf8ae3fa/>
<https://github.com/kotet/qiita-to-jekyll>

## Jekyllに配置

postフォルダを_postsに
 何でもよさそうだけどqiitaにした
imageをassetsに
 imageをqiitaに変更


一度公開した記事は限定公開にできない・・・
<https://help.qiita.com/ja/articles/qiita-private-article>




```
import urllib.parse
import urllib.request
import json

access_token = "XXXXXXXXXX"
url = "https://qiita.com/api/v2/authenticated_user/items?per_page=100"    
headers = {
    "Authorization": "Bearer " + access_token
}
req = urllib.request.Request(url, headers=headers)
result = urllib.request.urlopen(req).read().decode()
items = json.loads(result)

# items = [items[0]]

for item in items:
    item["body"] = "記事の内容はこちらを参照してください。\n" + "https://XXXXXXXXXX/" + item["id"] + "/" + "\n<!--\n\n" + item["body"] + "\n\n-->"

    headers = {
        "Authorization": "Bearer " + access_token,
        "Content-Type": "application/json"
    }

    url = "https://qiita.com/api/v2/items/" + item["id"]
    req = urllib.request.Request(url, json.dumps(item).encode(), headers=headers, method='PATCH')
    result = urllib.request.urlopen(req).read().decode()
    # print(result)
```

コメントアウトしている
100記事
1記事で試してみてほしい
