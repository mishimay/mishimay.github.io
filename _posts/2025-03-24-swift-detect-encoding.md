---
title: "[Swift] Data型の文字列のエンコーディングを自動で検知してStringへ変換する"
layout: post
category: プログラミング
tag:
- ios
- swift
---


```swift
func string(from data: Data) -> String? {
    var nsString: NSString?
    guard case let rawValue = NSString.stringEncoding(for: data, encodingOptions: nil, convertedString: &nsString, usedLossyConversion: nil), rawValue != 0 else { return nil }
    return String(data: data, encoding: String.Encoding(rawValue: rawValue))
}
```

## 参考

<https://stackoverflow.com/questions/59832706/how-to-detect-encoding-in-data-based-on-a-string>
