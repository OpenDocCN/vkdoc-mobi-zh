# 第 20 章 ■ 模型-视图-控制器模式

## 绘制边界

Cocoa 中的矩形，以及任何在给定边界内进行绘制的方法，总是在矩形的坐标边界内进行绘制。图 20-12 展示了填充一个`3×5`矩形的情况。

***图 20-12.** 在 Swing 与 Cocoa 中填充矩形* 在 Java 中，使用宽度为 1 像素的画笔通过`Graphics2D.fillRect(Shape)`绘制的矩形，其绘制范围会超出矩形边界 1 像素。而在 Cocoa 中，仅填充矩形内部的像素。填充该矩形的代码为`NSRectFill(NSMakeRect(0.0,0.0,3.0,5.0))`。

## 绘制线条与形状

Java 提供了许多用于即时绘制线条、矩形、椭圆和其他形状的图元。

Cocoa 仅提供了一组主要用于填充矩形的 C 函数集合，以及功能丰富的`NSBezierPath`类。`NSBezierPath`是用于绘制任何类型的线条、几何形状或两者结合的通用对象。

一个`NSBezierPath`可以由一个或多个线段组成。线段可以是直线或曲线、实线或虚线。线段可以连接或不连接。它们可以形成封闭形状（如星形或椭圆形）或开放形状（如弧形）。一旦定义完成，贝塞尔路径对象可以被填充（绘制形状内部）或描边（绘制定义形状的线条）。该路径拥有许多属性，用于控制线条连接处和端点的绘制方式。它还包含一个“缠绕规则”，用于在线段相互交叉时确定形状的内部区域。图 20-11 中的线条是使用代码清单 20-2 中的代码绘制的。

**代码清单 20-2.** 绘制一条 5.0 像素宽的线条

```
NSBezierPath *path = [NSBezierPath new];
[path moveToPoint:NSMakePoint(0.0,0.0)];
[path lineToPoint:NSMakePoint(0.0,5.0)];
[path setLineWidth:3.0];
[path stroke];
```

存在一些便捷构造函数，用于创建形成椭圆或圆角矩形的贝塞尔路径。

[www.it-ebooks.info](http://www.it-ebooks.info/)

#### 自定义视图

自定义视图对象是通过继承`NSView`类创建的，其方式几乎与在 Swing 中继承`JComponent`完全相同。你的`NSView`类必须至少提供以下内容：

*   一个`-(id)initWithFrame:(NSRect)frame`初始化方法。这是所有`NSView`子类的指定初始化方法。
*   可选地重写`-(void)drawRect:(NSRect)rect`方法，以提供视图内部的自定义绘制。此方法等价于`javax.swing.JComponent.paint(Graphics)`。

代码清单 20-3 中的代码演示了一个自定义的`NSView`子类，它绘制了存储在`Chalkboard.png`文件中的图像。（这是井字棋项目中`ChalkboardView`类的原始版本，在添加动画功能之前。）

**代码清单 20-3.** 自定义`NSView`绘制方法

```
- (void)drawRect:(NSRect)rect
{
    NSImage *chalkboardImage = [NSImage imageNamed:@"Chalkboard"];
    NSRect imageRect;
    imageRect.origin = NSMakePoint(0.0,0.0);
    imageRect.size = [chalkboardImage size];
    [chalkboardImage drawInRect:[self bounds]
                      fromRect:imageRect
                     operation:NSCompositeSourceOver
                      fraction:1.0];
}
```

### 视图的失效与绘制

Cocoa 中的绘制与 Swing 及类似 GUI 框架中的绘制几乎相同。当视图的内容需要重绘时，其占据的区域会被标记为失效（invalidated）。要使`NSView`失效，可将`needsDisplay`属性设置为`YES`，或发送`-setNeedsDisplayInRect:`消息。`AppKit`框架会将视图的区域或子区域添加到用户界面中需要重绘的聚合区域中。

最终，应用程序框架会向每个失效的视图对象发送一条`-drawRect:`消息。该消息包含需要绘制的特定视图子区域。除非你的视图非常复杂，否则可以忽略此信息；所有绘制都会自动裁剪到失效区域。

> **注意** `-drawRect:`方法仅负责绘制其内容。视图实际上是在接收到`-display`消息时才进行绘制的。这是一个高级消息，它会递归地向自身及其所有子视图发送`-drawRect:`消息。通常你不会重写`-display`，并且通常也绝不会向视图对象发送`-display`消息。要重绘一个视图，请设置其`needsDisplay`属性，让框架将其添加到需要更新的视图对象队列中，然后等待接收`-drawRect:`消息。

[www.it-ebooks.info](http://www.it-ebooks.info/)

### 图形上下文

在 Java 中，组件用于绘制自身的图形对象会作为参数传递给`JComponent.paint(Graphics)`方法。在 Cocoa 中，所有绘制都在全局`NSGraphicsContext`对象的隐式上下文中进行。框架会在发送`-drawRect:`方法之前为你的视图准备好上下文。你的视图在其`bounds`属性定义的局部坐标系中进行绘制。在`-drawRect:`方法返回后，你不应对绘图上下文做任何假设。

`NSGraphicsContext`定义了许多适用于所有绘制命令的属性：

*   裁剪区域
*   绘制颜色
*   描边（画笔）颜色
*   填充颜色
*   字体
*   阴影
*   仿射变换

与`java.awt.Graphics`的组织方式截然不同，设置这些属性的方法分散在定义它们的各个类中。表 20-2 列出了在 Cocoa 框架中查找`java.awt.Graphics2D`属性近似等价物的位置。

***表 20-2.** 图形上下文属性设置*

| `java.awt.Graphics2D` | Cocoa 函数或方法 |
| :--- | :--- |
| `setClip(int,int,int,int)` | `NSRectClip(…)`, `NSRectClipList(…)` |
| `clipRect(int,int,int,int)` | `-[NSBezierPath addClip]` |
| `setColor(Color)` | `-[NSColor set]`, `-[NSColor setStroke]`, `-[NSColor setFill]` |
| `setBackground(Color)` | 无：`NSEraseRect(…)`始终用白色绘制。使用`NSRectFill(…)`填充矩形。 |
| `setFont(Font)` | `-[NSFont set]` |
| `setShadow` | `-[NSShadow set]` |
| `setTransform(AffineTransform)` | `-[NSAffineTransform set]` |
| `setComposite(Composite)` | `-[NSGraphicsContext setCompositingOperation:]` |

设置并使用这些属性与 Java 相同：设置所需属性，然后调用绘制命令。绘制命令将使用当前图形上下文的适用属性，如代码清单 20-4 所示。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**代码清单 20-4.** 设置并使用图形上下文属性

```
NSBezierPath *path = …
[[NSColor blueColor] setFill];    // 将填充颜色设置为蓝色
[[NSColor greenColor] setStroke]; // 将描边颜色设置为绿色
[path fill];                      // 填充一个蓝色形状
[path stroke];                    // 绘制一条绿色线条
```

图形上下文还包含一组松散的深奥属性和渲染提示，与`Graphics2D`类似，它们影响缩放、抗锯齿、色彩空间调整等。

`Graphics2D`的某些属性并非 Cocoa 中图形上下文的属性。相反，它们是定义对象的属性。例如，`NSBezierPath`所绘制的线条的宽度和形状是贝塞尔路径对象的属性，而非图形上下文的属性。



