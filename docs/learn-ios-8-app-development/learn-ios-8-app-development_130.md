# Swift 内存管理

## 函数签名

```
func drawAtPoint(_ point: CGPoint, blendMode mode: CGBlendMode, alpha alpha: CGFloat)
```

我已经在两者中突出显示了关键部分。这两个函数的签名如下：

```
drawAtPoint:blendMode:alpha:
```

请注意，局部参数名不是签名的一部分。只有函数名和外部参数名才是重要的。当你想在 Swift 中明确标识一个函数时，你只使用函数名和外部参数名来编写该函数，就像这样：`drawAtPoint(_:,blendMode:,alpha:)`。

方法签名仍然在 Cocoa Touch 框架中使用，尽管由于闭包的出现，它们的使用正在减少。如果你确实需要指定一个，你可以将其格式化为字符串。（Swift 缺少 Objective-C 使用的特殊方法选择器类型。）你在第 11 章的 Shapely 应用中就这样做过，如下所示：

```
let pan = UIPanGestureRecognizer(target: self, action: "moveShape:")
```

`action` 参数是一个方法选择器，以 Swift 字符串的形式编写，它使得该手势识别器在激活时调用 `moveShape(_:)` 函数。

**注意：** 选择器中的冒号表示参数。如果你的类有两个函数，`setNeedsIceCream()`（无参）和 `setNeedsIceCream(_ want: Bool)`（有一个参数），它们的签名通过尾随冒号来区分：`setNeedsIceCream` 与 `setNeedsIceCream:`。

