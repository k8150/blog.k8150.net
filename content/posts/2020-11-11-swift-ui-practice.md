---
author: "koji haigo"
title: "SwiftUI 徹底入門"
date: "2020-11-22"
description: ""
tags: ["iOS", "SwiftUI"]
---

開始：2020/11/12(水)  
終了：2020/11/22(日)

## 雑感

- 状態の持ち方が理想的。
- IBOutlet, IBActionを使わなくて良くなるのは嬉しい。
- propetyWrapper, projectedProperty, Bindingの使い方が分かった。

## Chapter01 メモ

### PropetyWrapper

```swift
@propertyWrapper
struct UserDefault<T> {
    let key: String
    let defaultValue: T
    var wrappedValue: T {
        get {
            return UserDefaults.standard.object(forKey: key) as? T ?? defaultValue
        }
        set {
            UserDefaults.standard.set(newValue, forKey: key)
        }
    }
}

enum GlobalSettings {
    @UserDefault(key: "FOOD_FEATURE_ENABLED", defaultValue: false)
    static var isFoodFeatureEnabled: Bool
    @UserDefault(key: "BAR_FEATURE_VALUE", defaultValue: -1)
    static var barFeatureValue: Int
}
```

### Function builders

> VStack, HStackのクロージャで要素を追加する方法はこのやり方で実現している。

```swift
@_functionBuilder
public struct StringBuilder {
    public static func buildBlock(_ a: String, _ b: String) -> String {
        a + b
    }
    public static func buildBlock(_ a: String, _ b: String, _ c: String) -> String {
        a + b + c
    }
}

@StringBuilder func ab() -> String {
    "あの"
    "イーハトーヴォの"
}
var str0 = ab() // print: あのイーハトーヴォの

@StringBuilder func abc() -> String {
    "あの"
    "イーハトーヴォの"
    "すきとおった風、"
}
var str1 = abc()  // print: あのイーハトーヴォのすきとおった風、
```

## Chapter02 メモ

### 標準のモディファイア

|名称|説明|その他|
|:-|:-|:-|
|frame|フレームのサイズとビューの配置位置を指定する。||
|fixedSize|ビューの表示を固定する。||
|border|境界線を表示する。||
|position|ビューの中心をその親の座標空間ないの指定された座標に固定する。||
|offset|元の位置を基準としてビューの表示位置をずらす。||
|edgesIgnoringSafeArea|指定されたへんに対して、ビューの配置可能領域をセーフエリアの外へ拡張する。||
|padding|ビューのすべての辺から指定されあt値だけ余白を追加します。||
|background|ビューの背景を設定する。||
|foregroundColor|ビューの前景要素に使う色を指定する。||
|overlay|ビューの前に別のビューを重ねて配置します。||
|clipped|ビューをフレームの教会で切り取る。||
|clipShape|ビューの切り取り形状を設定する。||
|mask|引数のビューを使ってマスクする。||
|cornerRadius|指定されたコーナーの半径でビューを角丸にクリッピングする。||
|Textビュー|- lineLimit<br>- truncationMode<br>- lineSpacing<br>- font||
|Imageビュー|- resizable<br>- scaledToFit<br>- scaledToFill||

## Chapter03 メモ

### ビューの構築にif文を使う

```swift
// ↓返却する型が違うためエラーになる
if Bool.random() {
    return Image("win-image")
} else {
    return Text("コンピュータの勝ちです。")
}
// ↓これだとエラーにならない。
// 返却する型が異なるときは**Group**を使用する。
Group {
    if Bool.random() {
        Image("win-image")
    } else {
        Text("コンピュータの勝ちです。")
    }     
}
```

## Chapter04 メモ

```swift
struct ContentView: View {
    @State var showDetails = false
    var body: some View {
        VStack {
            Button(action: {
                self.showDetails.toggle()
            }) {
                Text("詳細表示")
            }
            if showDetails {
                Text("詳しくはWebで")
                    .font(.largeTitle)
                    .lineLimit(nil)
            }
        }
    }
}
```

Toggleビュー

```swift
struct ContentView: View {
    @State var showText = false
    var body: some View {
        VStack {
            Toggle(isOn: $showText) {
                Text("テキストを表示する")
            }
            if showText {
                Text("Hello World.")
            }
        }
    }
}
```

TextFieldビュー

```swift
struct ContentView: View {
    @State var name = ""
    var body: some View {
        VStack {
            TextField("氏名を入力してください", text: $name)
            text("こんにちは！\(name)")
        }
    }
}
```

Pickerビュー

```swift
struct ContentView: View {
    @State var weather = 1
    var body: some View {
        Picker(selection: $weathre, label: Text("Weathre : \(weather)")) {
            Text("Sunny").tag(1)
            Text("Cloudy").tag(2)
            Text("Rainy").tag(3)
        }
    }
}
```

Pickerビュー(配列)

```swift
struct ContentView: View {
    var susi = ["まぐろ", "サーモン", "いくら", "エビ", "穴子"]
    @State var var selected = 0
    var body: some View {
        VStack {
            Picker(selection: $selected, label: Text("寿司ネタを選択してください")) {
                ForEach(0..<susi.count) {
                    Text(self.susi[$0])
                }
            }
            Text("あなたの寿司 : \(susi[selected])")
        }
    }
}
```

## Chapter05 メモ

- ListView
- Identifiableプロトコル

```swift
// Identifiableプロトコルを継承する
struct Restaurant: Identifiable {
    var id = UUID()
    var title: String
    var type: String
    var note: String
    var rating: Int
}

// List要素の構造体
struct RestaurantRow: View {
    var restaurant: Restaurant
    var body: some View {
        RestaurantView(
            title: restaurant.title,
            type: restaurant.type,
            note: restaurant.note,
            rating: restaurant.rating
        )
    }
}

struct ContentView: View {
    var body: some View {
        let restaurants = [
            Restaurant(
                title: "壱江島",
                type: "居酒屋",
                note: "",
                rating: 0
            ),
            ...
        ]
        /**
        * ↓シンプルな書き方
        * return List(restaurants, rowContent: RestaurantRow.init)
        **/
        return List(restaurants) { restaurant in
            RestaurantRow(restaurant: restaurant)
        }
    }
}
```

## Chapter06 メモ

- Shapeプロトコルのこと。
- 飛ばし気味に読んだ。実践投入時に詳細を調べる。

## Chapter07 メモ

- tabBarの使い勝手がかなり好印象
- 画面遷移の基本は押さえられた。~~EnvironmetnValuesだけ理解があやしい。~~ ←後続章で解説有り。

navigationBarTitle

```swift
struct ContentView: View {
    var body: some View {
        NavigationView {
            Text("Hello SwiftUI!")
                .navigationBarTitle("Navigationタイトル")
            Text("メニューを表示するには右諏訪イプします")
        }
    }
}
```

EnvironmetnValues

```swift
struct ContentView: View {
    @State var mfgs = ["Kawasaki", "Honda", "Yamaha", "Suzuki", "Ducati", "BMW"]

    var body: some View {
        NavigationView {
            List {
                ForEach(mfgs, id: \.self) { mfg in 
                    NavigationLink(destination: CustomBackView(name: mfg) {
                        Text(mfg)
                    })
                }
            }
            .navigationBarTitle("Manufacturing")
        }
    }
}

struct CustomBackView: View {
    @Environment(\.presentaionMode) var presentaionMode
    var name: String = "Hello SiwftUI!"

    var body: some View {
        VStack {
            Text(name).font(.system(size: 30))
            Button(action: {
                self.presentaionModel.wrappedValue.dismiss()
            }) {
                Text("戻る")
            }
        }
        .navigationBarBackButtonHidden(true)
    }
}
```

TabBar

```swift
struct ContentView: View {
    var body: some View {
        TabView {
            Text("FirstContent")
                .tabItem { Text("First") }
            Text("SectondContent")
                .tabItem { Text("Second") }
        }
    }
}
```

## Chapter08 メモ

- Alert, ActionSheetについて
- 特に特筆することはなし

## Chapter09 メモ

- アニメーションとエフェクトについて
- アニメーションはUIKitよりも簡易的に設定できるようになった印象。

## Chapter10 メモ

状態変数(propetyWrapper)

```swift
@State var count = 0
```

射影プロパティ(projectedProperty)

```swift
$count
```

Binding変数

```swift
@Binding var value: Int
```

ObservedObject

```swift
class MyData: ObservableObject {
    @Published var score = 0
}

struct ContentView: View {
    @ObservedObject var data: MyData

    var body: some View {
        VStack {
            Text("\(data.score)")
            Button(action: {
                self.data.score += 1
            }) {
                Text("カウントアップ")
            }
        }
    }
}
```

状態の更新をSwiftUIに手動で送信する

```swift
import SwiftUI
import Combine

class MyData: ObservableObject {
    let objectWillChange = ObservableObjectPublisher()
    var score = 0 {
        willSet {
            objectWillChange.send()
        }
    }
}
```

EnvironmentObject

> 任意のビュー参照可能にする

```swift
// SceneDelegate.swiftに下記の通り設定する
let order = Order()
let contentView = ContentView().environmentObject(order)

// 他のViewで以下のようにアクセスできる
@EnvironmentObject var order: Order
```

Environment

> アプリの環境値をenvironmentメソッドで設定することができる

## Chapter11 メモ

- キーボードの監視は従来のUIKitと同じ

イベントとジェスチャ

SwiftUIのライフサイクル

```swift
// View表示時
.onAppear
// View非表示時
.onDisappear
// ソフトウェアキーボードを監視する
func startObserve() {
    NotificationCenter
        .default
        .addObserver(
            self,
            selector: #selector(keyboardWillChangeFrame),
            name: UIResponder.keyboradWillChangeFrameNotification,
            object: nil)
}

func stopObserve() {
    NotificationCenter
        .default
        .removeObserver(
            self,
            name: UIResponder.keyboardWillChangeFrameNotification,
            object: nil)
}

@objc
func keyboardWillChangeFrame(_ notification: Notification) {
    if
        let keyboardEndFrame = notification.userInfo?[UIResponder.keyboardFrameEndUserInfoKey] as? NSValue,
        let keyboardBeginFrame = notification.userInfo?[UIResponder.keyboardFrameBeginUserInfoKey] as? NSValue 
        {
            let endMinY = keyboardEndFrame.cgRectValue.minY
            let beginMinY = keyboardBeginFrame.cgRectValue.minY
            keyboardHeight = beginMinY - endMinY
            if keyboardHeight < 0 {
                keyboardHeight = 0
            }
        }
}
```

## Chapter12 メモ

UIKitでSwiftUIを使う

```swift
let nextView = UIHostingController(rootView: 
    VStack {
        ...
    }
)
self.present(nextView, animated: true, completion: nil)
```

SwiftUIからUIKitを使う

```swift
struct ActivityIndicator: UIViewRepresentable {

    public var style = UIActivityIndicatorView.Style.medium
    @Binding var animating: Bool

    func makeUIView(context: Context) -> UIActivityIndicatorView {
        return UIActivityIndicatorView(style: style)
    }

    func updateUIView(_ uiView: UIActivityIndicatorView, context: Context) {
        if animating {
            uiView.startAnimating()
        } else {
            uiView.stopAnimating()
        }
    }
}

struct ContentView: View {
    @State var animating = false

    var body: some View {
        VStack {
            Text("UIActivityIndicatorView")
            ActivityIndicator(animating: .constant(true))
            ActivityIndicator(animating: $animating)
            ActivityIndicator(style: .larg, animating: $animating)
            Toggle(isOn: $animating) {
                Text("インジケータの回転")
            }
        }
    }
}
```

## Chapter13 メモ

SwiftUIからCoreDataを使う

## Chapter14 メモ

ヒント

- ローカライズ手順
- モディファイアを作成する
