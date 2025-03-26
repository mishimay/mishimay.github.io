---
title: "[SwiftUI] Windows間で状態を共有したいとき・したくないとき"
layout: post
category: プログラミング
tag:
- ios
- swiftui
---

Windows間で共有したいときは `App` のstructに@Stateプロパティとして定義する。  
共有したくないときは `WindowGroup` の中のViewに定義する。

NotificationCenterを使ったりしないといけないのかと思ったが簡単なことだった。


```swift
@main
struct MyApp: App {
    @State private var someObject = SomeObject()

    var body: some Scene {
        WindowGroup {
            ContentView(someObject: $someObject)
        }
    }
}
```

## 環境
Xcode Version 16.2 (16C5032a)
