---
title: "[CloudKit] CKSubscriptionの通知を受け取るために必要そうなこと・必要じゃなさそうなこと"
layout: post
category: プログラミング
tag:
- ios
- cloudkit
---


特定のCKRecordの変更を検知しようとしたときに試したこと。


## 必要そうなこと

1. 以下のようなsubscriptionを登録するメソッドを用意
  - subscriptionIDは何でも良さそう

    ```swift
    func saveSubscription(recordID: CKRecord.ID, recordType: CKRecord.RecordType) async throws {
        let predicate = NSPredicate(format: "recordID = %@", recordID)
        let subscription = CKQuerySubscription(
            recordType: recordType,
            predicate: predicate,
            subscriptionID: "myID",
            options: [.firesOnRecordUpdate]
        )
        let notificationInfo = CKSubscription.NotificationInfo()
        notificationInfo.shouldSendContentAvailable = true
        subscription.notificationInfo = notificationInfo
        try await database.save(subscription)
    }
    ```


2. 1をアプリ起動時に呼ぶ


3. 通知の受け取り


    ```swift
    func application(_ application: UIApplication, didReceiveRemoteNotification userInfo: [AnyHashable : Any]) async -> UIBackgroundFetchResult {
        if let ckNotification = CKNotification(fromRemoteNotificationDictionary: userInfo) {
            // 受け取り後の処理
        }
        return .newData
    }
    ```


4. Background Modes の設定
  - XcodeのCapabilityで "Background Modes" を追加
  - "Remote notifications" にチェックを入れる
  - なぜかMac用ビルド（Designed for iPad）では必要なかった


5. Xcodeからのビルドでシミュレーターでsubscriptionするコードを実行した後、"Cloud Kit" サイト上で "Deploy Schema Changes…" してProductionに反映させる

    - その際、変更差分として特に何も表示されないが必要な作業らしい

    > Create subscriptions in the development environment first and then promote them to production. Attempting to create a subscription directly in the production environment results in an error.

    <https://developer.apple.com/documentation/cloudkit/cksubscription>



## 必要じゃなさそうなこと

- `UIApplication.shared.registerForRemoteNotifications()` を呼ぶ

- プッシュ通知用の証明書やKeyの生成

- その他、Apple Developer サイト上での操作

  > You don’t need to enable push notifications for the app’s explicit App ID in your developer account at developer.apple.com to receive subscription notifications. Xcode automatically adds the APNs entitlement to your entitlement file when you enable CloudKit.

  <https://developer.apple.com/documentation/cloudkit/cksubscription>



## 環境
Xcode Version 16.2 (16C5032a)
