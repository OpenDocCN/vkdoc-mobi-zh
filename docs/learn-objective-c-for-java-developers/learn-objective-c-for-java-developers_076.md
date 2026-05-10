# NSGraphicsContext 的状态管理

`NSGraphicsContext` 有一个 `compositingOperation` 属性，用于那些未将显式合成模式作为参数的绘图命令。

## 图形上下文状态栈

你可能需要多个配置不同的图形上下文来完成绘图任务。撤销对任何一个图形上下文所做的更改并不总是容易的，所有后续的绘图操作都会受到任何更改的影响，因此当前上下文很容易被“污染”。这时图形上下文状态栈就派上了用场。

图形上下文的当前状态可以被推送，将其所有属性保存在一个按线程划分的栈中。然后你可以对图形上下文进行任何想要的更改。当之前的状态被恢复时，自保存以来所做的所有更改都会被丢弃。这在设置复杂属性（如裁剪、阴影和仿射变换）时特别有用，这些属性通常只适用于少数绘图命令。`Listing 20-5` 演示了推送和恢复图形上下文的基本模式。

**Listing 20-5.** 保存和恢复图形上下文

```
NSGraphicsContext *currentContext = [NSGraphicsContext currentContext];
NSRect circleRect = NSMakeRect(0.0, 0.0, 20.0, 20.0);

NSBezierPath *path = [NSBezierPath bezierPathWithOvalInRect:circleRect];

// 填充圆的内部（带投影）
[currentContext saveGraphicsState]; // 推送上下文状态
NSShadow *shadow = [NSShadow new];  // 创建阴影
[shadow setShadowColor:[NSColor blackColor]];
[shadow setShadowOffset:NSMakeSize(3.0, -3.0)];
[shadow setShadowBlurRadius:1.5];
[shadow set];                      // 为所有绘图设置阴影
[[NSColor whiteColor] setFill];    // 将填充颜色设为白色
[path fill];                       // 绘制白色圆形 + 阴影
[currentContext restoreGraphicsState]; // 恢复之前的上下文状态

// 绘制圆形轮廓（不带阴影）
[[NSColor blueColor] setStroke];
[path stroke];
```

你必须确保每个 `-saveGraphicsState` 消息都有一个对应的 `-restoreGraphicsState` 消息。

## 绘图工具

与图形上下文属性类似，绘制对象的方法分散在各个对象自身之中。例外的是少数用于填充矩形或绘制多部分图像的 C 函数。最常用的 C 函数列在 `Table 20-3` 中。

**Table 20-3.** 常见的 Cocoa 绘图函数

| AppKit 绘图函数 | 描述 |
| --- | --- |
| `NSEraseRect(NSRect)` | 用白色填充一个矩形。 |
| `NSRectFill(NSRect)` | 用当前颜色填充一个矩形。 |
| `NSRectFillUsingOperation(NSRect, NSCompositingOperation)` | 使用指定的合成模式填充一个矩形。 |
| `NSRectFillList(NSRect*, NSInteger)` | 用当前颜色填充一个矩形列表。 |
| `NSFrameRect(NSRect)` | 在矩形边界内部绘制一条宽度为 `1.0` 的线，颜色为当前颜色。 |
| `NSFrameRectWithWidth(NSRect, CGFloat)` | 在矩形内部绘制一条指定宽度的线，颜色为当前颜色。 |

大约有三十个这样的函数。它们大多是实用工具，用于简化绘制如由多个图像构成的可调整大小按钮、浮雕边缘、投影、虚线轮廓（用于指示选择）等对象。完整的列表请参考《Application Kit Functions Reference》¹。

其余绘图方法在定义它们的对象中实现，有时通过分类来实现。例如，`AppKit` 框架定义了一个分类，为 `NSString` 类附加了一个 `-drawAtPoint:WithAttributes:` 方法。`Table 20-4` 列出了常见的 Java 绘图方法及在 Cocoa 框架中能找到的近似对应项。

¹ Apple Inc., *Application Kit Functions Reference*, http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/Miscellaneous/AppKit_Functions/Reference/, 2008.

**Table 20-4.** 常见的绘图方法

| `java.awt.Graphics2D` | Cocoa 函数或方法 |
| --- | --- |
| `drawImage(Image, int, int, int, int, int, int, int, ...)` | `-[NSImage` |



