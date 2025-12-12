---
title: "[SwiftUI] 月相を描く"
layout: post
category: プログラミング
tag:
- ios
- swiftui
---

<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">だからそこに星を描くなと何回言えばわかるんですか？ <a href="https://t.co/ax7Po0X5h4">pic.twitter.com/ax7Po0X5h4</a></p>&mdash; kameji@西野美容室 (@KamejiNishibi) <a href="https://twitter.com/KamejiNishibi/status/1677843884242771968?ref_src=twsrc%5Etfw">July 9, 2023</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>


## 実際とは異なる月相とは？

- 円を円で切り抜く or 半円に重ねる
- その他それらしい曲線で円を削ぐ or 半円を膨らませる


## 正しい月相とは？

- 月は（地球の影に入るとき以外は）太陽に半分だけ照らされていて、半球に見える
- 半球の平面部分は円である
- 円は斜めから見ると楕円になる

つまり周は円を使い、楕円で切り抜く or 楕円を重ねる ことでより正確な月相を描くことができる。


## コード例

```swift
import SwiftUI

struct MoonShape: Shape {
    let phase: Angle

    func path(in rect: CGRect) -> Path {
        let center = CGPoint(x: rect.midX, y: rect.midY)
        let radius = min(rect.width, rect.height) / 2
        let sin = sin(phase.radians)
        let cos = cos(phase.radians)
        let ellipseWidth = cos * radius * 2

        var halfCircle = Path()
        halfCircle.addRelativeArc(center: center, radius: radius, startAngle: .degrees(sin > 0 ? -90 : 90), delta: .degrees(180))
        halfCircle.closeSubpath()

        var ellipse = Path()
        ellipse.addEllipse(in: CGRect(x: center.x - ellipseWidth / 2, y: center.y - radius, width: ellipseWidth, height: radius * 2))
        ellipse.closeSubpath()

        return cos > 0 ? halfCircle.subtracting(ellipse) : halfCircle.union(ellipse)
    }
}
```

phaseの値は以下の「黄経差」に準拠。
- <https://eco.mtk.nao.ac.jp/koyomi/wiki/B7EEA4CECBFEA4C1B7E7A4B1.html>


## 使用例

![MoonShapeの使用例](/assets/posts/2025/2025-12-12-moon-shape.png)
