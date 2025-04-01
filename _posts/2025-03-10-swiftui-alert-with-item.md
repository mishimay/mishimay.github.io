---
title: "[SwiftUI] Deprecatedになった `alert(item:content:)` を置き換える"
layout: post
category: プログラミング
tag:
- ios
- swiftui
---

itemのありなしでalertの表示/非表示をコントロールでき、またそのitemを引数に取れる `alert(item:content:)` メソッドが非常に便利だったのですがなぜかDeprecatedになりました。

<https://developer.apple.com/documentation/swiftui/view/alert(item:content:)>


`sheet(item:onDismiss:content:)` や `fullScreenCover(item:onDismiss:content:)` はまだなぜか生きています。

<https://developer.apple.com/documentation/swiftui/view-presentation>


## 置き換え後のコード例

```swift
    // ...
    @State private var deleteItem: Item?
    @State private var isDeleteAlertPresented = false

    var body: some View {
        VStack {
            // ...
            Button(role: .destructive) {
                deleteItem = item
            } label: {
                Label("Delete", systemImage: "trash")
            }
            // ...
        }
        .alert("Confirmation", isPresented: $isDeleteAlertPresented, presenting: deleteItem) { deleteItem in
            Button(role: .destructive) {
                itemTree.removeItem(id: deleteItem.id)
            } label: {
                Text("Delete")
            }
        } message: { deleteItem in
            Text("Do you delete \(deleteItem.name)?")
        }
        .onChange(of: deleteItem) { oldValue, newValue in
            isDeleteAlertPresented = newValue != nil
        }
        .onChange(of: isDeleteAlertPresented) { oldValue, newValue in
            if !newValue {
                deleteItem = nil
            }
        }
    }
```


## Modifierを作る

```swift
import SwiftUI

struct AlertModifier<A, M, T>: ViewModifier where A: View, M: View, T: Equatable {
    @Binding var item: T?
    @State private var isPresented = false
    let title: LocalizedStringKey
    let actions: (T) -> A
    let message: (T) -> M

    func body(content: Content) -> some View {
        content
            .alert(title, isPresented: $isPresented, presenting: item, actions: actions, message: message)
            .onChange(of: item) { oldValue, newValue in
                isPresented = newValue != nil
            }
            .onChange(of: isPresented) { oldValue, newValue in
                if !newValue {
                    item = nil
                }
            }
    }
}

extension View {
    func alert<A, M, T>(item: Binding<T?>, title: LocalizedStringKey, @ViewBuilder actions: @escaping (T) -> A, @ViewBuilder message: @escaping (T) -> M) -> some View where A: View, M: View, T: Equatable {
        modifier(AlertModifier(item: item, title: title, actions: actions, message: message))
    }
}

```


## 環境
Xcode Version 16.2 (16C5032a)
