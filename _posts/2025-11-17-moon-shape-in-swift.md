---
title: "[SwiftUI] 月相を描く"
layout: post
category: プログラミング
tag:
- ios
- swiftui
---

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
