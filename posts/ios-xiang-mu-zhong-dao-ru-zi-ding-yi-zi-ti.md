---
title: '🪷 iOS 项目中导入自定义字体'
date: 2023-03-28 05:33:25
tags: []
published: true
hideInList: false
feature: /post-images/ios-xiang-mu-zhong-dao-ru-zi-ding-yi-zi-ti.png
isTop: false
---

## 1. 下载字体文件

字体文件可以从 [https://fonts.google.com/](https://fonts.google.com/) 这类网站下载，此处就不列举。

---

## 2. 导入字体文件

> 一定要勾选 `Copy items if needed` 哦，不勾选的话导入的只是个文件位置引用。

![](https://seaony.github.io/post-images/1679953001044.png)

---

## 3. 修改 Info.plist 文件

打开你的 `Info.plist` 文件，新增一项：`Fonts provided by application`。

对应的值即为你的字体文件的名称 (不需要带 Group 前缀，需要带文件后缀)。

![](https://seaony.github.io/post-images/1679953202754.png)

---

## 4. 确保字体文件已添加至资源文件

确保你的字体文件已添加至 `TARGETS` - `Build Phases` - `Copy Bundle Resources` 中。

> 一般导入字体文件后会自动添加，但也会有意外 :-)

![](https://seaony.github.io/post-images/1679953445072.png)

---

## 5. 打印出来看看

运行这段代码即可打印出所有可用字体，可以检查一下是否导入成功

```swift
for fontFamily in UIFont.familyNames {
    print(fontFamily)

    for font in UIFont.fontNames(forFamilyName: fontFamily) {
        print(fontFamily + ": " + font)
    }
}
```

![](https://seaony.github.io/post-images/1679953569638.png)