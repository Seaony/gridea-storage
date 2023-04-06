---
title: '👨‍💻 iOS 项目中使用自定义字体'
date: 2023-03-28 05:33:25
tags: [Swift,iOS]
published: true
hideInList: false
feature: /post-images/ios-xiang-mu-zhong-dao-ru-zi-ding-yi-zi-ti.png
isTop: false
---

# 🪴 全局加载字体

## 1. 导入字体文件

准备好要使用的字体文件，并且将它导入至你的 XCode Project 中。

> Tips：一定要勾选 `Copy items if needed` ，不勾选的话只会进行位置引用。

![](https://seaony.github.io/post-images/1679953001044.png)

---

## 2. 修改 Info.plist

打开你的 `Info.plist` 文件，新增一项：`Fonts provided by application`。

对应的值即为你的字体文件的名称 (不需要带 Group 前缀，需要带文件后缀)。

![](https://seaony.github.io/post-images/1679953202754.png)

---

## 3. 添加资源文件

确保你的字体文件已添加至 `TARGETS` - `Build Phases` - `Copy Bundle Resources` 中。

> 一般导入字体文件后会自动添加，但也会有意外 :-)

![](https://seaony.github.io/post-images/1679953445072.png)

---

## 4. 确认导入成功

运行这段代码即可打印出所有可用字体，检查是否导入成功。

```swift
for fontFamily in UIFont.familyNames {
    print(fontFamily)

    for font in UIFont.fontNames(forFamilyName: fontFamily) {
        print(fontFamily + ": " + font)
    }
}
```

---

# 🧐 覆盖默认字体

如果你需要全局都显示某个字体，那么就需要这样设置。

如果不这样做，那么你需要在每一个显示文本的地方都指定 `FontName`。

> Tips：使用覆盖 UIFont 的方式设置全局字体会导致键盘等系统 UI 字体也被替换。

## 1. 创建 UIFont 拓展

在 `Extension` Group 下创建 `UIFontExtension` 文件拓展 UIFont 类。

> 当然，你也可以放在别的位置，使用其他名称 🫠。

```swift
import UIKit

// MARK: - AppFontName
struct AppFontName {
    static let italic = "Montserrat-Medium"
    static let regular = "Montserrat-Medium"
    static let semibold = "Montserrat-SemiBold"
    static let bold = "Montserrat-Bold"
    static let heavy = "Montserrat-ExtraBold"
    static let black = "Montserrat-Black"
}

// MARK: - UIFontDescriptor.AttributeName
extension UIFontDescriptor.AttributeName {

    /// NSCTFontUIUsageAttribute
    static let nsctFontUIUsage = UIFontDescriptor.AttributeName(rawValue: "NSCTFontUIUsageAttribute")

}

/// 字体扩展
extension UIFont {

    /// 是否已经替换过
    static var isOverrided: Bool = false

    // 重写系统字体
    @objc class func mySystemFont(ofSize size: CGFloat, weight: UIFont.Weight) -> UIFont {
        switch weight {
        case .ultraLight, .thin, .light, .regular, .medium:
            return UIFont(name: AppFontName.regular, size: size)!
        case .semibold:
            return UIFont(name: AppFontName.semibold, size: size)!
        case .bold:
            return UIFont(name: AppFontName.bold, size: size)!
        case .heavy:
            return UIFont(name: AppFontName.heavy, size: size)!
        case .black:
            return UIFont(name: AppFontName.black, size: size)!
        default:
            return UIFont(name: AppFontName.regular, size: size)!
        }
    }

    // 重写粗体字体
    @objc class func myBoldSystemFont(ofSize size: CGFloat) -> UIFont {
        return UIFont(name: AppFontName.bold, size: size)!
    }

    // 重写斜体字体
    @objc class func myItalicSystemFont(ofSize size: CGFloat) -> UIFont {
        return UIFont(name: AppFontName.italic, size: size)!
    }

    // 重写字体的编码方法
    @objc convenience init(myCoder aDecoder: NSCoder) {
        guard
                let fontDescriptor = aDecoder.decodeObject(forKey: "UIFontDescriptor") as? UIFontDescriptor,
                let fontAttribute = fontDescriptor.fontAttributes[.nsctFontUIUsage] as? String
        else {
            self.init(myCoder: aDecoder)
            return
        }
        var fontName = ""
        switch fontAttribute {
        case "CTFontRegularUsage":
            fontName = AppFontName.regular
        case "CTFontEmphasizedUsage", "CTFontBoldUsage":
            fontName = AppFontName.bold
        case "CTFontObliqueUsage":
            fontName = AppFontName.italic
        default:
            fontName = AppFontName.regular
        }
        self.init(name: fontName, size: fontDescriptor.pointSize)!
    }

    /// 替换系统字体
    class func overrideInitialize() {

        // 避免 method swizzling 运行两次
        guard self == UIFont.self, !isOverrided else {
            return
        }

        // 避免 method swizzling 运行两次并恢复到原始初始化函数
        isOverrided = true

        // 替换系统字体
        if let systemFontMethod = class_getClassMethod(self, #selector(systemFont(ofSize:weight:))),
           let mySystemFontMethod = class_getClassMethod(self, #selector(mySystemFont(ofSize:weight:))) {
            method_exchangeImplementations(systemFontMethod, mySystemFontMethod)
        }

        // 替换粗体字体
        if let boldSystemFontMethod = class_getClassMethod(self, #selector(boldSystemFont(ofSize:))),
           let myBoldSystemFontMethod = class_getClassMethod(self, #selector(myBoldSystemFont(ofSize:))) {
            method_exchangeImplementations(boldSystemFontMethod, myBoldSystemFontMethod)
        }

        // 替换斜体字体
        if let italicSystemFontMethod = class_getClassMethod(self, #selector(italicSystemFont(ofSize:))),
           let myItalicSystemFontMethod = class_getClassMethod(self, #selector(myItalicSystemFont(ofSize:))) {
            method_exchangeImplementations(italicSystemFontMethod, myItalicSystemFontMethod)
        }

        // Trick to get over the lack of UIFont.init(coder:))
        if let initCoderMethod = class_getInstanceMethod(self, #selector(UIFontDescriptor.init(coder:))),
           let myInitCoderMethod = class_getInstanceMethod(self, #selector(UIFont.init(myCoder:))) {
            method_exchangeImplementations(initCoderMethod, myInitCoderMethod)
        }
    }
}
```

---

## 2. 覆盖初始化方法

在你的 `AppDelegate` 文件中注册如下方法，覆盖 UIFont 初始化。

```swift
override init() {
    super.init()
    UIFont.overrideInitialize()
}
```

---

# 🫠 Reference Links

- [Set default custom font for entire app — Swift 5](https://medium.com/fabcoding/set-default-custom-font-for-entire-app-swift-5-55709e36e6f3)

- [Setting up custom font style for Text, Labels and Buttons globally in iOS Application — iOS Swift](https://medium.com/@arunvaishy1007/setting-up-custom-font-style-for-text-labels-and-buttons-globally-in-ios-application-ios-swift-17b08317c026)


