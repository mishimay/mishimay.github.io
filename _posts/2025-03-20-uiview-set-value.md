---
title: "[UIKit] UIViewに任意のオブジェクトを保持させる"
layout: post
category: プログラミング
tag:
- ios
- uikit
---

サブクラスを作成すれば良いのですが、そこまでするのが面倒なときにUIViewのCALayerにkey-value方式で自由に値をセットできます。

```swift
uiView.layer.setValue("someValue", forKey: "someKey")
let value = uiView.layer.value(forKey: "someKey") as? String
```

これは公式に認められている方法のようです。

> The CAAnimation and CALayer classes are key-value coding compliant container classes, which means that you can set values for arbitrary keys. Even if the key someKey is not a declared property of the CALayer class, you can still set a value for it as follows:

Key-Value Coding Extensions  
<https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/CoreAnimation_guide/Key-ValueCodingExtensions/Key-ValueCodingExtensions.html>


## 参考

- <https://stackoverflow.com/questions/399987/how-do-i-attatch-a-key-value-pair-to-a-uiview-on-iphone/400251#400251>
- <https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/CoreAnimation_guide/Key-ValueCodingExtensions/Key-ValueCodingExtensions.html>


## 環境
Xcode Version 16.2 (16C5032a)
