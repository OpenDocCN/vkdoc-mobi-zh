# 第 9 章：用户界面设计

然而，当您与设计师合作时，他们通常会面临一个困境。为了支持 Retina 显示屏的 iPhone，他们需要创建一个 640×960 像素的 Photoshop 文档。但不可避免地，他们可能会为 Retina 显示屏尺寸设计一个按钮、标题或徽标，却使用了非标准尺寸。这会产生问题，因为 Retina 显示屏尺寸的图像在每一维度上必须精确地是非 Retina 显示屏对应图像的两倍。正确的做法是为 320×480 像素的屏幕设计用户界面元素，然后按精确的两倍尺寸生成它们。

非图像元素（如文本）应由系统自动以适当尺寸显示。

#### iPad 上的视图布局

另一个可能导致视图布局代码改变的因素是在 iPad 上运行。同一款应用既可以运行在 iPad 上，也可以运行在 iPhone 上，因此有时您需要在代码中判断当前运行的设备。为此，可以使用 `UIDevice` 类，它代表了应用运行所在的设备，示例如下：

```
BOOL isPad = ([[UIDevice currentDevice] userInterfaceIdiom] == UIUserInterfaceIdiomPad);
```

`UIDevice` 的 `userInterfaceIdiom` 方法根据设备返回 `UIUserInterfaceIdiomPad` 或 `UIUserInterfaceIdiomPhone`（在 iPod touch 上返回后者）。此外，还有一个有用的宏 `UI_USER_INTERFACE_IDIOM()`，它替代了对 `UIDevice` 的调用，并将调用包装在安全检查中，以防您的代码需要在 iOS 3.1.3 或更早版本（这些版本没有 `UIDevice` 的 `userInterfaceIdiom` 方法）上运行。上一行代码也可使用该宏改写为：

```
BOOL isPad = (UI_USER_INTERFACE_IDIOM() == UIUserInterfaceIdiomPad);
```

与 iPhone 类似，第三代 iPad 的 Retina 显示屏使用 `@2x` 后缀来标识高分辨率图形。按照惯例，后缀 `~ipad` 常用于表示文件的 iPad 版本，而 `~iphone` 后缀（或无后缀）则用于 iPhone 版本。因此，单个图像可能有四种变体：

- `blueButton~iphone.png`
- `blueButton~iphone@2x.png`



