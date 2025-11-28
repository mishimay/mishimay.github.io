---
title: "[Swift] Dateは何を表しているか"
layout: post
category: プログラミング
tag:
- ios
- swift
---


## 主な時刻の定義

- TAI（国際原子時）
  - **原子の性質に基づいて1秒が決定**される
  - 実際の原子時計に基づいている
  - TTという理想を実現する一つの時刻系

- TT（地球時）
  - 天文学などで使用される理論上の理想
  - TAIと同様に1秒が厳密に進む
  - `TT = TAI + 32.184秒` の関係

- UT（世界時）
  - **地球の自転に基づいて1秒が決定**される
  - 自転速度が一定ではないのでTAIやTTのように1秒が一定ではない
  - GMTの前身
    - GMTという用語の使用を非推奨とする勧告が出ているが、一般的には未だにUT1やUTCと同じ意味で使われることが多い

- UT1
  - UTはUT0/UT1/UT2に細分される
  - UT1は地球の自転量をもっとも忠実に表す
  - UT0とUT2は現在ほとんど使われていない

- UTC（協定世界時）
  - TAIと同じ正確な秒数を刻むが、UT1とのずれが0.9秒以上大きくなると閏秒が追加または削除される
  - ずれが出てくるのは地球の自転の時間にブレがあるため
  - 日常生活で一般的に使われている時刻系


### 地球の自転について

- 地軸のふらつきや大気の影響などで地球の自転速度にはブレが生じるため一定ではない
- 長期的には月の潮汐力の影響で遅くなる傾向がある
- さまざまな要素が関係してくるため正確に予測することが困難
  - 国立天文台の朔弦望計算ページでいくつかの論文を基にしたΔTの予測値を表示できるが、数十年先になると数分程の違いが生じてくる
    - <https://eco.mtk.nao.ac.jp/cgi-bin/koyomi/cande/phenomena_py.cgi>


### ΔTについて

- `ΔＴ = TT - UT1`
- 「原子的に厳密に進む時刻」と「地球の自転を基にした時刻」との差
- 月の朔弦望時間の計算に必要
  - 月と太陽と地球との位置関係はTTによって計算されるが、それが地球上で何時何分何秒になるかはUT1に依るため（と理解しています）
  - 地球の自転速度は予測が難しいためΔTの予測も困難
    - 実際に問題になるケースとしては、旧暦（日本における太陰太陽暦、すなわち天保暦）の未来の日付の決定ができない
      - 朔を含む日を各月の一日とするため


## SwiftのDateは何を表しているか？

基本的にはUTCと同期しているが閏秒がない。
つまりTAIでもTTでもUT1でもUTCでもない時刻表現。

Dateの表す日時がTAIやUTCの時刻系でいつを指すのかは後述のようにタイムサーバーの実装にも依るので曖昧である。概念に近いものだと思う。

- DateはOSの時刻と同じ時刻を表す
  - OSの時刻はタイムサーバー（NTP）を通じてUTCを基にした時刻と同期している
- ただしUTCにはある閏秒をDateは考慮していない
  - もしUTCのように閏秒が追加されているなら以下のようなコードの結果がぴったり00秒にならない
  ```swift
  print(Date(timeIntervalSince1970: 60 * 60 * 24 * 365 * 56).formatted(date: .abbreviated, time: .complete)) // 18 Dec 2025 at 9:00:00 AM GMT+9
  ```
- 閏秒がないことで閏秒を考慮せずに時刻を扱えるというプログラミング上のメリットがある
  - 1日が必ず `60 * 60 * 24 秒` になる
- 近年では閏秒を避けるためにタイムサーバーにおいて Leap Smear という手法が使われているらしい
  - 前後の秒数を伸び縮みさせて閏秒を吸収させる
  - よってそれらの秒数は原子時計による厳密な1秒ではなくなる
    - つまりUTCとずれが出て、僅かだがUTCとは違う時刻になる
  - 2008年にGoogleが最初に導入したらしい
  - Appleのタイムサーバー (time.apple.com) が対応しているかは判らなかった
- Leap Smear がないタイムサーバーの場合は？
  - 「60秒」が追加される場合は次の時間の「0秒」とみなされる？
  - 「59秒」が2回繰り返される？
  - いずれにしても同じ秒が2回繰り返されることになりその間の処理によっては不具合が起こりそう


## 参考文献

国立天文台　暦Wiki
- 国際原子時 <https://eco.mtk.nao.ac.jp/koyomi/wiki/B9F1BADDB8B6BBD2BBFE.html>
- 地球時 <https://eco.mtk.nao.ac.jp/koyomi/wiki/C3CFB5E5BBFE.html>
- 世界時 <https://eco.mtk.nao.ac.jp/koyomi/wiki/C0A4B3A6BBFE.html>
- 協定世界時 <https://eco.mtk.nao.ac.jp/koyomi/wiki/B6A8C4EAC0A4B3A6BBFE.html>
- 地球の自転のふらつき <https://eco.mtk.nao.ac.jp/koyomi/wiki/BBFEB9EF2FC3CFB5E5A4CEBCABC5BEA4CEA4D5A4E9A4C4A4AD.html>
- ΔT
  - <https://eco.mtk.nao.ac.jp/koyomi/wiki/A6A4A3D4.html>
  - <https://eco.mtk.nao.ac.jp/koyomi/wiki/A6A4A3D42FBFE4C4EA.html>
  - <https://eco.mtk.nao.ac.jp/koyomi/wiki/A6A4A3D42FCDBDC2AC.html>
