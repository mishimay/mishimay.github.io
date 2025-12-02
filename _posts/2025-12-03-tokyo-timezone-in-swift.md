---
title: "[Swift] TimeZone(identifier: \"Asia/Tokyo\") ≠ TimeZone(secondsFromGMT: 60 * 60 * 9)"
layout: post
category: プログラミング
tag:
- ios
- swift
---


`TimeZone(identifier: "Asia/Tokyo")` のオフセット値は+9時間とは限らない。


## 〜1887年（地方時である東京時刻）

**+9時間18分59秒**

- 日本標準時（JST）が導入されるまでは日本各地に地方時が定められていた
- より正確には+9時間19分1秒らしい
  - 基準となる場所が「天守台」か「天文台」かで間違えられた？
    - <https://www.m3tech.blog/entry/timezone-091859>
- ちなみに新暦まで（=明治の始めまで）は京都を基準に旧暦を定めていた
  - 京都基準だった時代については `TimeZone(identifier: "Asia/Tokyo")` に反映されていない


## 1948年〜1951年（サマータイム導入）

**サマータイム期間中は+10時間**

- 戦後すぐサマータイムが導入されていた時期があった
- 1年の中での期間は _5月（or4月）の第1土曜日の翌日_ から _9月の第2土曜日_ まで
- 「日本標準時はJSTで表すが、夏時刻の適用期間はJDTとなる。」（ウィキペディアより）とのことだが、`TimeZone` での区別は特になさそう
  - `TimeZone(abbreviation: "JDT")` は`nil`になる


## 今後について

- 再び日本にサマータイムが導入される可能性
- あるいは日本標準時の基準が東経135度から変わる可能性もなくはない


## オフセットの定義はどこにあるのか？

- 言語内に持っておらずOSの設定ファイルを参照しているらしい
  - <https://github.com/swiftlang/swift-corelibs-foundation/blob/265274a4be41b3d4d74fe4626d970898e4df330f/Sources/CoreFoundation/CFTimeZone.c#L51>
- 以下のコマンドで見れる
  - `zdump -v /var/db/timezone/zoneinfo/Asia/Tokyo`


## イニシャライゼーションによる違い

- 上述の通り、歴史的経緯が反映されたオフセット値になる
  - `TimeZone(identifier: "Asia/Tokyo")`
  - `TimeZone(abbreviation: "JST")` 

- オフセットが必ず+9時間になる
  - `TimeZone(secondsFromGMT: 60 * 60 * 9)`
  - `TimeZone(abbreviation: "GMT+9")`


## 実際に問題になるケース

- 同じDateでも日時表示に違いが生じる
  - 例えば日の出日の入り時刻を表示したいときにサマータイム時刻か+9時間かで違う
- 存在しない時間がある
  - サマータイム開始時に時計の針を1時間進める → その間の1時間は時刻として存在しない
- 2度繰り返される時間がある
  - サマータイム終了時に時計の針を1時間早める → 1時間同じ時刻が繰り返される
  - タイムゾーンを併記することで区別できる
    - JST or JDT、 UTC+9 or UTC+10、 標準時間 or 夏時間　など

これらの問題は今現在サマータイムを導入している国でも起きる。


## サンプルコード

```swift
let tokyoCalendar: Calendar = {
    var calendar = Calendar(identifier: .gregorian)
    calendar.timeZone = TimeZone(identifier: "Asia/Tokyo")!
    return calendar
}()
let jstCalendar: Calendar = {
    var calendar = Calendar(identifier: .gregorian)
    calendar.timeZone = TimeZone(abbreviation: "JST")!
    return calendar
}()
let plus9Calendar: Calendar = {
    var calendar = Calendar(identifier: .gregorian)
    calendar.timeZone = TimeZone(secondsFromGMT: 60 * 60 * 9)!
    return calendar
}()
let gmt9Calendar: Calendar = {
    var calendar = Calendar(identifier: .gregorian)
    calendar.timeZone = TimeZone(abbreviation: "GMT+9")!
    return calendar
}()
let tokyoDateFormatter: DateFormatter = {
    let formatter = DateFormatter()
    formatter.timeZone = TimeZone(identifier: "Asia/Tokyo")!
    formatter.locale = Locale(identifier: "ja_JP")
    formatter.dateStyle = .medium
    formatter.timeStyle = .medium
    return formatter
}()

do {
    let components = DateComponents(year: 1887, month: 12, day: 31, hour: 23, minute: 59)
    print(tokyoDateFormatter.string(from: tokyoCalendar.date(from: components)!))
    print(tokyoDateFormatter.string(from: jstCalendar.date(from: components)!))
    print(tokyoDateFormatter.string(from: plus9Calendar.date(from: components)!))
    print(tokyoDateFormatter.string(from: gmt9Calendar.date(from: components)!))
}
do {
    let components = DateComponents(year: 1948, month: 5, day: 2, hour: 1, minute: 0)
    print(tokyoDateFormatter.string(from: tokyoCalendar.date(from: components)!))
    print(tokyoDateFormatter.string(from: jstCalendar.date(from: components)!))
    print(tokyoDateFormatter.string(from: plus9Calendar.date(from: components)!))
    print(tokyoDateFormatter.string(from: gmt9Calendar.date(from: components)!))
}
```


## 参考文献

国立天文台　暦Wiki
- 日本の本初子午線 <https://eco.mtk.nao.ac.jp/koyomi/wiki/BBFEB9EF2FC6FCCBDCA4CECBDCBDE9BBD2B8E1C0FE.html>

ウィキペディア
- 夏時刻法 <https://ja.wikipedia.org/wiki/%E5%A4%8F%E6%99%82%E5%88%BB%E6%B3%95>

18分59秒をめぐって日本標準時の歴史をひもとくことに
<https://www.m3tech.blog/entry/timezone-091859>
