---
title: '🪴 Xcode 中创建纯代码布局项目'
date: 2023-03-23 00:03:28
tags: [Gridea,Swift]
published: true
hideInList: false
feature: /post-images/hello-gridea.png
isTop: false
---

## 1：移除 Main.storyboard


```shell
rm ./Main.storyboard
```

---

## 2：修改 Main Interface

> 因为 Xcode 版本更新，不同的版本会有不同的处理方案。

-  Xcode Version < **14.0**

    清空 `General` - `Deployment Info` - `Main Interface`。

-  Xcode Version >= **14.0**

    清空 `Build Setting` - `UIKit Main Storyboard File Base Name`。


---

## 3：设置默认 ViewController

在你的 `AppDelegate.swift` - `application` 中添加如下代码。

```swift
// 设置默认显示的界面
let controller = YourDefaultViewController()
        
self.window = UIWindow(frame: UIScreen.main.bounds)
window!.rootViewController = UINavigationController(rootViewController: controller)
window?.makeKeyAndVisible()
```