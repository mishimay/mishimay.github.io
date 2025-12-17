---
title: "[SwiftUI] iOS 26 以上でもTextを連結したい"
layout: post
category: プログラミング
tag:
- ios
- swiftui
---


## 問題点

`Text("A") + Text("B")` というような書き方が iOS 26 以上ではdeprecatedになった。


```
'+' was deprecated in iOS 26.0: Use string interpolation on `Text` instead: `Text("Hello \(name)")`
```

- 配列などから動的に `Text` を生成したい場合に困る
- 文字列だけなら `"A" + "B"` と連結された文字列を作って `Text` に入れれば良い
- だが `Text` に `Image` をinterpolationしたい場合にうまくいかない

```swift
let strings = ["\(Image(systemName: "moon"))", "A", "B"]
Text(strings.joined())
// 画像ではなくdescriptionのような文字列が表示される
```

## 解決策

以下のように `Text` 自体を `Text` にinterpolationできて、そうすれば問題は起きなそう。

```swift
let texts = [Text("\(Image(systemName: "moon"))"), Text("A"), Text("B")]
texts.reduce(Text("")) { Text("\($0)\($1)") }
```
