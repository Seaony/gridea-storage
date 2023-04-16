---
title: '🎃 iOS Swift 项目实现多语言支持'
date: 2023-04-16 19:11:43
tags: [Swift,iOS]
published: true
hideInList: false
feature: /post-images/ios-swift-xiang-mu-shi-xian-duo-yu-yan-zhi-chi.png
isTop: false
---

## 😶‍🌫️ 创建文件

在项目根目录下右键选择 `New File`，创建一个名为 `Localizable.strings` 的 Strings File。

![](https://seaony.github.io/post-images/1681668230963.png)

在 `Localizable.strings` 中添加你的多语言文本，它看起来就像这样。

```swift
// 示例文本
ExampleMessage = "Example Message";
```
## 🪴 使用文本

接下来，你可以像这样使用它：
```swift
/// - NSlocalizedString
NSlocalizedString("ExampleMessage", comment: "")
 // Example Message

 /// - R.swift
 R.string.localizable.exampleMessage()
  // Example Message
```
---

## 👨‍💻 启用国际化

选中项目 `Project Info`，在 `Localizations` 中点击加号，添加你想要支持的语言。

![](https://seaony.github.io/post-images/1681668335176.png)

---

## 📚 App 名称国际化

创建 `InfoPlist.strings` 文件，点击右侧 `localize`，然后选择对应的语言。

接着在文件中设置对应语言的 App 名称:

```swift
CFBundleName="Your App Name"
```