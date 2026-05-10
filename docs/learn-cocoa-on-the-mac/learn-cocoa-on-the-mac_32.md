# 15. 高级绘图主题

## 摘要

第 14 章介绍了 Cocoa 关键绘图概念的基础知识，例如使用路径描述形状、将图像复制到屏幕以及渲染文本。本章将在此基础上展开，展示一些让图形“活”起来的技巧。第一部分将介绍如何让视图响应鼠标事件，使用户能够与自定义视图交互。第二部分将简要介绍 Core Animation，这项令人兴奋的技术只需几行代码就能实现流畅的动画效果。

## 编辑曲线

第 14 章介绍了如何使用 `NSBezierPath` 类绘制圆角矩形、椭圆、直线和点。如果你曾在 Photoshop 或其他应用中使用过贝塞尔绘图工具，你可能会好奇这些形状与贝塞尔曲线究竟有何关系！实际上，贝塞尔曲线本质上是由一系列描述路径的点，以及描述点之间曲线的控制点构成的。因此，任何能用笔（在现实世界中，或在计算机图形系统中虚拟地）绘制的形状，都可以被描述为贝塞尔曲线，包括直线和折线。

不过，对于普通人来说，“贝塞尔曲线”通常更接近于图 15-1 所示的形态。图中黑色曲线是一条贝塞尔曲线，由两个端点（左下角和右上角）以及两个控制点（图中较大的圆圈，位于杆的末端）定义。用户通过拖拽控制点可以改变曲线的形状。这种视图可以用作速度控制，用于确定某个值随时间变化的速率，例如物体从一个点到另一个点的运动。将曲线变为直线可以指定完全线性的过渡，而将其调整为 S 形则可以使值开始变化缓慢，中间快速上升，最后在接近目标值时逐渐放缓（有时称为“缓入/缓出”过渡）。本节我们将要实现的就是这种控制。

![A978-1-4302-4543-8_15_Fig1_HTML.jpg](img/A978-1-4302-4543-8_15_Fig1_HTML.jpg)

**图 15-1.** 一条贝塞尔曲线

### 准备工作

在 Xcode 中创建一个新的 Cocoa 项目，并将其命名为 `CurveEdit`。确保勾选“自动引用计数”（Automatic Reference Counting），并将此应用的类前缀设置为 `CE`。

该应用的核心部分将全部位于视图对象中，但首先让我们处理好视图的基础架构。本项目将采用 MVC 架构，这有助于确保我们创建的视图能够作为独立组件使用。用于显示每个控制点 x 和 y 值的文本字段将通过 Cocoa 绑定连接到控制器对象，自定义视图本身也是如此。本应用中的模型将只是控制器类中的一组实例变量，但如果我们选择使用或创建真实的模型对象，所有视图也同样可以轻松绑定到该模型上。

创建一个名为 `CECurveView` 的新 `NSView` 子类，暂时保持其实现内容不变。我们稍后会回来处理它。打开 `MainMenu.xib`。从对象库中拖出一个自定义视图（Custom View），然后使用身份检查器（Identity Inspector）将其类设置为 `CECurveView`。将其大小调整为大约 240 x 240。接下来，我们需要连接应用代理以及刚刚拖入窗口的视图。为此，在辅助编辑器中打开 `CEAppDelegate.h` 文件，按住 Control 键从 `CECurveView` 自定义视图拖拽到辅助编辑器窗口中的 `CEAppDelegate.h` 代码区域。将拖拽点放到现有 `@property` 声明的正下方，此时应出现“插入输出口”（Insert Outlet）提示。将新输出口命名为 `curveView`。

我们还需要做几处修改。我们将添加一个导入语句，以引入 `CECurveView` 头文件，并添加一些稍后要通过 Cocoa 绑定使用的额外属性。请在 `.h` 文件中添加以下内容：

```
#import <Cocoa/Cocoa.h>
#import "CECurveView.h"

@interface CEAppDelegate : NSObject <NSApplicationDelegate>

@property (assign) IBOutlet NSWindow *window;
@property (weak) IBOutlet CECurveView *curveView;
@property (assign) CGFloat cp1X;
@property (assign) CGFloat cp1Y;
@property (assign) CGFloat cp2X;
@property (assign) CGFloat cp2Y;

@end
```

然后切换到 `.m` 文件，并添加以下代码行：

```
#import "CEAppDelegate.h"

@implementation CEAppDelegate

- (void)applicationDidFinishLaunching:(NSNotification *)aNotification {
    // 使 CECurveView 注意到我的变化
    [self.curveView bind:@"cp1X" toObject:self withKeyPath:@"cp1X"
                options:nil];
    [self.curveView bind:@"cp1Y" toObject:self withKeyPath:@"cp1Y"
                options:nil];
    [self.curveView bind:@"cp2X" toObject:self withKeyPath:@"cp2X"
                options:nil];
    [self.curveView bind:@"cp2Y" toObject:self withKeyPath:@"cp2Y"
                options:nil];
    
    // 使我注意到 CECurveView 的变化
    [self bind:@"cp1X" toObject:self.curveView withKeyPath:@"cp1X"
        options:nil];
    [self bind:@"cp1Y" toObject:self.curveView withKeyPath:@"cp1Y"
        options:nil];
    [self bind:@"cp2X" toObject:self.curveView withKeyPath:@"cp2X"
        options:nil];
    [self bind:@"cp2Y" toObject:self.curveView withKeyPath:@"cp2Y"
        options:nil];
    
    // 设置初始值
    self.cp1X = 0.5;
    self.cp1Y = 0.0;
    self.cp2X = 0.5;
    self.cp2Y = 1.0;
}

@end
```

如你所见，控制器类非常简单。它所做的仅仅是声明用于访问控制点 x 和 y 值的属性，一个用于连接到 `CECurveView` 本身的 `IBOutlet`，为我们的 `curveView` 建立一些绑定（因为无法在 Interface Builder 中完成这些操作），并设置控制点的一些默认起始值。由于我们是通过代码设置该对象的绑定，因此（就 nib 文件而言）`CECurveView` 实例现在已完全就绪。至此，我们可以关闭辅助编辑器了。



返回对象库，拖出一个`Label`，将其放置在`CECurveView`下方。将标签的标题改为`X1:`。再拖出一个`文本字段`，放在标签旁边，直到蓝色辅助线显示对齐为止。同时选中标签和文本字段，然后按下`⌘D`复制它们。将复制的这组元素放在第一组下方，并将标签的标题改为`Y1:`。这个表单将显示贝塞尔曲线中第一个控制点的`x`和`y`值。为两个文本字段分别创建绑定。对于每个字段，我们都需要使用模型键路径`cp1X`和`cp1Y`，将其`Value`属性绑定到`CEAppDelegate`对象。

同时选中两个标签和两个文本字段，复制它们（`⌘-D`），并将新副本放在第一组元素的右侧。这些字段将显示第二个控制点的值，因此将标签重命名为`X2:`和`Y2:`，并设置与第一组类似的绑定，但使用`cp2X`和`cp2Y`作为键路径。

参考图 15-1 查看应有的效果，合理排列布局，并调整窗口大小以匹配新增内容。保存文件，运行项目，确保此时没有错误。生成的应用程序将只允许我们编辑四个文本字段的内容。它还会提示`CECurveView`不符合键`cp1X`的键值编码要求，这确实没错：我们尚未将其添加到`CECurveView`中，但即将进行修改！

### 贝塞尔曲线基础设施

让我们从建立一些基础设施开始编写`CECurveView`类。`CECurveView`需要跟踪两个控制点，我们将它们设置为四个浮点数，每个浮点数通过一个属性访问，就像我们为控制器类所做的那样。我们还想使用类似于第 14 章中为 MrSmiley 使用的技术，使 GUI 能够根据渲染大小自适应缩放。这次，我们将设置固定的边界，以便始终可以在平面上`(0,0)`到`(1,1)`之间的正方形内绘制曲线，并留出一些额外的周边空间。因此，我们将添加一些代码，将边界设置为`(-0.1,-0.1)`到`(1.1,1.1)`的正方形，并且无论视图如何调整大小，都保持这些边界不变。通过添加下面粗体显示的代码来完成所有这些操作：

```objc
// CECurveView.h:

#import <Cocoa/Cocoa.h>

@interface CECurveView : NSView {

  NSRect myBounds;

}

@property (assign, nonatomic) CGFloat cp1X;
@property (assign, nonatomic) CGFloat cp1Y;
@property (assign, nonatomic) CGFloat cp2X;
@property (assign, nonatomic) CGFloat cp2Y;

@end
```

然后将这些代码添加到`.m`文件中：

```objc
// CECurveView.m:

#import "CECurveView.h"

@implementation CECurveView

- (void)setCp1X:(CGFloat)f {
  self.cp1X = MAX(MIN(f, 1.0), 0.0);
  [self setNeedsDisplay:YES];
}

- (void)setCp1Y:(CGFloat)f {
  self.cp1Y = MAX(MIN(f, 1.0), 0.0);
  [self setNeedsDisplay:YES];
}

- (void)setCp2X:(CGFloat)f {
  self.cp2X = MAX(MIN(f, 1.0), 0.0);
  [self setNeedsDisplay:YES];
}

- (void)setCp2Y:(CGFloat)f {
  self.cp2Y = MAX(MIN(f, 1.0), 0.0);
  [self setNeedsDisplay:YES];
}

- (id)initWithFrame:(NSRect)frame {
  self = [super initWithFrame:frame];
  if (self) {
    // 初始化代码写在这里。
    myBounds = NSMakeRect(-0.1, -0.1, 1.2, 1.2);
    [self setBounds:myBounds];
  }
  return self;
}

- (void)setFrameSize:(NSSize)newSize {
  [super setFrameSize:newSize];
  [self setBounds:myBounds];
}

- (void)drawRect:(NSRect)rect {
  // 绘制代码写在这里。
}

@end
```

请注意，我们正在为这些属性实现自己的设置器，因此需要将属性标记为`nonatomic`。编译器会注意到我们没有实现自己的获取器，因此编译器仍会为我们生成这些获取器。在设置器中，我们将对输入值施加一个范围限制，使其限制在`0.0`到`1.0`之间。我们还将使用`setNeedsDisplay:`将窗口标记为“脏”，强制系统在属性发生变化时重绘窗口。



### 绘制曲线

接下来进入有趣的部分：绘制曲线本身。我们将使用预处理器 `#defines` 来定义颜色和线宽的值，便于定位和调整外观。在 `CECurveView.m` 顶部附近添加以下代码：

```
#define CP_RADIUS 0.1
#define CP_DIAMETER (CP_RADIUS*2)
#define BACKGROUND_COLOR [NSColor whiteColor]
#define GRID_STROKE_COLOR [NSColor lightGrayColor]
#define GRID_FILL_COLOR [NSColor colorWithCalibratedWhite:0.9 alpha:1.0]
#define CURVE_COLOR [NSColor blackColor]
#define LINE_TO_CP_COLOR [NSColor darkGrayColor]
#define CP_GRADIENT_COLOR1 [NSColor lightGrayColor]
#define CP_GRADIENT_COLOR2 [NSColor darkGrayColor]
```

我们将按照以下示例实现 `drawControlPointAtX:y:` 和 `drawRect:` 方法。绘制控制点的代码演示了 `NSGradient` 类的使用，该类可用于填充贝塞尔路径内部，而不仅仅是纯色填充。

```
- (void)drawControlPointAtX:(CGFloat)x y:(CGFloat)y {
    NSBezierPath *cp = [NSBezierPath bezierPathWithOvalInRect:
        NSMakeRect(x - CP_RADIUS, y - CP_RADIUS,
        CP_DIAMETER, CP_DIAMETER)];
    NSGradient *g;
    g = [[NSGradient alloc] initWithStartingColor:CP_GRADIENT_COLOR1
        endingColor:CP_GRADIENT_COLOR2];
    [g drawInBezierPath:cp
        relativeCenterPosition:NSMakePoint(0.0, 0.0)];
}

- (void)drawRect:(NSRect)rect {
    [NSGraphicsContext saveGraphicsState];

    // 绘制背景
    NSBezierPath *bg = [NSBezierPath bezierPathWithRoundedRect:myBounds
        xRadius:0.1 yRadius:0.1];
    [BACKGROUND_COLOR set];
    [bg fill];

    // 绘制网格
    NSBezierPath *grid1 = [NSBezierPath bezierPath];
    [grid1 moveToPoint:NSMakePoint(0.0, 0.0)];
    [grid1 lineToPoint:NSMakePoint(1.0, 0.0)];
    [grid1 lineToPoint:NSMakePoint(1.0, 1.0)];
    [grid1 lineToPoint:NSMakePoint(0.0, 1.0)];
    [grid1 lineToPoint:NSMakePoint(0.0, 0.0)];
    [grid1 moveToPoint:NSMakePoint(0.5, 0.0)];
    [grid1 lineToPoint:NSMakePoint(0.5, 1.0)];
    [grid1 moveToPoint:NSMakePoint(0.0, 0.5)];
    [grid1 lineToPoint:NSMakePoint(1.0, 0.5)];
    [GRID_FILL_COLOR set];
    [grid1 fill];
    [GRID_STROKE_COLOR set];
    [grid1 setLineWidth:0.01];
    [grid1 stroke];

    // 绘制指向控制点的连线
    NSBezierPath *cpLines = [NSBezierPath bezierPath];
    [cpLines moveToPoint:NSMakePoint(0.0, 0.0)];
    [cpLines lineToPoint:NSMakePoint(self.cp1X, self.cp1Y)];
    [cpLines moveToPoint:NSMakePoint(1.0, 1.0)];
    [cpLines lineToPoint:NSMakePoint(self.cp2X, self.cp2Y)];
    [LINE_TO_CP_COLOR set];
    [cpLines setLineWidth:0.01];
    [cpLines stroke];

    // 绘制曲线本身
    NSBezierPath *bp = [NSBezierPath bezierPath];
    [bp moveToPoint:NSMakePoint(0.0, 0.0)];
    [bp curveToPoint:NSMakePoint(1.0, 1.0)
        controlPoint1:NSMakePoint(self.cp1X, self.cp1Y)
        controlPoint2:NSMakePoint(self.cp2X, self.cp2Y)];
    [CURVE_COLOR set];
    [bp setLineWidth:0.01];
    [bp stroke];

    // 绘制控制点
    [self drawControlPointAtX:self.cp1X y:self.cp1Y];
    [self drawControlPointAtX:self.cp2X y:self.cp2Y];

    [NSGraphicsContext restoreGraphicsState];
}
```

这种绘图代码可能会使方法变得相当冗长，但通常逻辑非常直接，正如前面展示的 `drawRect:` 方法。整个方法中没有一个循环或 `if` 结构！注意，与第 14 章中的笑脸符号代码不同，此绘图代码完全未引用视图的边界矩形。因为我们知道边界始终被调整为包含从 (0,0) 到 (1,1) 的方形区域，所以直接使用简单的硬编码值在该单位方形内及其周围绘制图形。

点击“运行”按钮启动应用，其外观应类似于图 15-1。我们应当能够编辑文本框中的数值（0.0 到 1.0 之间的任意值均可正常工作），并看到控制点和曲线随之相应变化。



### 监听鼠标

不过，在文本框中输入数值并非本练习的重点；我们的目标是拖拽那些控制点。事实证明，这一操作相当简单。`NSView` 包含了一些方法，当用户通过点击、拖拽等方式与视图交互时，这些方法会被自动调用。我们只需要重写几个方法，就能对每一次鼠标点击、拖拽和松开做出响应。

首先，在视图中添加一对 `BOOL` 类型的属性，用于追踪当前是否有控制点正在被拖拽。在 `CECurveView.h` 文件的 `@interface` 声明中，添加以下两行代码：

```
@property (assign) BOOL draggingCp1;
@property (assign) BOOL draggingCp2;
```

现在，我们在 `CECurveView.m` 文件的 `@implementation` 部分添加一些方法，以便开始拦截我们想要监听的鼠标活动。第一个方法是 `mouseDown:`，当用户在视图中点击时，它会被调用。

```
- (void)mouseDown:(NSEvent *)theEvent {

    // 获取当前鼠标位置，并转换到我们的坐标空间
    // （即由 bounds 表示的坐标空间）

    NSPoint mouseLocation = [theEvent locationInWindow];
    NSPoint convertedLocation = [self convertPoint:mouseLocation
                                          fromView:nil];

    // 检查点击是否发生在其中一个控制旋钮上
    NSPoint cp1 = NSMakePoint(self.cp1X, self.cp1Y);
    NSPoint cp2 = NSMakePoint(self.cp2X, self.cp2Y);

    if (pointsWithinDistance(self.cp1, convertedLocation, CP_RADIUS)) {
        self.draggingCp1 = YES;
    } else if (pointsWithinDistance(cp2, convertedLocation, CP_RADIUS)){
        self.draggingCp2 = YES;
    }

    [self setNeedsDisplay:YES];
}
```

在 `mouseDown:` 方法中，我们首先向窗口请求当前鼠标位置，然后使用内置的 `NSView` 方法将坐标从窗口的坐标系转换到我们自己的坐标系。这意味着，例如，在单位正方形右上角的点击（其初始值为距离窗口左下角的水平和垂直像素数），最终会被转换为 (1,1) 或附近的值。接着，我们进行两次测试，检查是否有控制点被点击。该测试使用了下面的函数，我们需要将它添加到 `CurveEdit.m` 文件的顶部，位于 `#defines` 组和 `@implementation` 部分之间：

```
static BOOL pointsWithinDistance(NSPoint p1, NSPoint p2, CGFloat d) {
    return pow((p1.x-p2.x), 2) + pow((p1.y - p2.y), 2) <= pow(d, 2);
}
```

`pointsWithinDistance` 函数利用勾股定理来判断两点（在我们的场景中，即控制点的中心和鼠标位置）之间的距离是否小于我们传入的距离（控制点半径）。通过这种方式，我们能够检查用户是否点击了某个控制点，如果是，就将相应的标志位（`draggingCp1` 或 `draggingCp2`）设置为 `YES`。

接下来需要实现的方法是 `mouseDragged:`，当鼠标按钮被按住并移动时，该方法会被反复调用。请注意，该方法并非在鼠标滑过的每个视图中都被调用，它总是在最初发生鼠标点击的视图中被调用；从某种意义上说，接收点击的那个视图“拥有”之后所有的拖拽事件。在这个方法中，我们再次从事件中获取鼠标位置，将其转换到我们视图自身的坐标系，然后更新当前正在被拖拽的控制点的坐标。如果没有任何控制点正在被拖拽，则什么也不会发生。

```
- (void)mouseDragged:(NSEvent *)theEvent {

    NSPoint mouseLocation = [theEvent locationInWindow];
    NSPoint convertedLocation = [self convertPoint:mouseLocation
                                          fromView:nil];

    if (self.draggingCp1) {
        self.cp1X = convertedLocation.x;
        self.cp1Y = convertedLocation.y;
    } else if (self.draggingCp2) {
        self.cp2X = convertedLocation.x;
        self.cp2Y = convertedLocation.y;
    }

    [self setNeedsDisplay:YES];
}
```

处理鼠标所需的最有一个方法是 `mouseUp:`，它让我们能够处理按钮松开的事件。与 `mouseDragged:` 类似，`mouseUp:` 也总是在发起拖拽的原始视图上被调用，这意味着用户在我们的视图中点击后，无论在哪里松开鼠标按钮，我们都会收到这条消息。这里我们仅需设置标志位，以指示没有任何东西正在被拖拽。

```
- (void)mouseUp:(NSEvent *)theEvent {
    self.draggingCp1 = NO;
    self.draggingCp2 = NO;
}
```

完成以上所有设置后，运行应用程序。我们应该能够拖拽控制点，曲线会随之移动，并且文本框中显示的数值也会在我们拖拽时同步更新。

### 一点小改进

这已经很酷了，但正如我们在尝试新的 GUI 设计时经常注意到的，随着使用的深入，一些改进会变得显而易见。首先，我们总是以相同的顺序绘制控制点，因此即使我们将控制点 1 拖拽到控制点 2 的正上方，控制点 2 也始终位于最上层。这感觉很不自然。幸运的是，修复这个问题非常简单，这也是我们将控制点绘制拆分成两个独立方法的真正实用原因。在 `drawRect:` 方法的末尾，添加如下粗体所示的行：

```
// 绘制控制点
if (self.draggingCp1) {
    [self drawControlPointAtX:self.cp2X y:self.cp2Y];
    [self drawControlPointAtX:self.cp1X y:self.cp1Y];
} else {
    [self drawControlPointAtX:self.cp1X y:self.cp1Y];
    [self drawControlPointAtX:self.cp2X y:self.cp2Y];
}
```

就是这样！点击运行。注意，当我们在拖拽第一个控制点时，它会显示在第二个控制点的前面。

如果能在拖拽过程中高亮显示当前被拖拽的控制点（例如使用不同的颜色绘制）也会很不错。这也是一项相当简单的修改，能为用户提供有用的视觉反馈。首先，为新的渐变定义一些高亮颜色，在文件顶部的其他 `#defines` 中添加以下代码行：

```
#define CP_GRADIENT_HIGHLIGHT_COLOR1 [NSColor whiteColor]
#define CP_GRADIENT_HIGHLIGHT_COLOR2 [NSColor redColor]
```

现在，修改 `drawControlPointAtX:y:` 方法，通过添加以下粗体代码行，增加一个参数来指定是否绘制高亮版本：

```
- (void)drawControlPointAtX:(CGFloat)x y:(CGFloat)y dragging:(BOOL)dragging {
    NSBezierPath *cp = [NSBezierPath bezierPathWithOvalInRect:
        NSMakeRect(x - CP_RADIUS, y - CP_RADIUS, CP_DIAMETER, CP_DIAMETER)];
    NSGradient *g;

    if (dragging) {
        g = [[NSGradient alloc] initWithStartingColor:CP_GRADIENT_HIGHLIGHT_COLOR1
                                          endingColor:CP_GRADIENT_HIGHLIGHT_COLOR2];
    } else {
        g = [[NSGradient alloc] initWithStartingColor:CP_GRADIENT_COLOR1
                                          endingColor:CP_GRADIENT_COLOR2];
    }

    [g drawInBezierPath:cp
       relativeCenterPosition:NSMakePoint(0.0, 0.0)];
}
```

由于我们给控制点绘制方法增加了一个参数，因此还需要相应修改 `drawRect:` 末尾调用它的方式，如下所示：

```
// 绘制控制点
if (self.draggingCp1) {
    [self drawControlPointAtX:self.cp2X y:self.cp2Y dragging:self.draggingCp2];
    [self drawControlPointAtX:self.cp1X y:self.cp1Y dragging:self.draggingCp1];
} else {
    [self drawControlPointAtX:self.cp1X y:cp1Y dragging:self.draggingCp1];
    [self drawControlPointAtX:self.cp2X y:self.cp2Y dragging:self.draggingCp2];
}
```

现在点击运行，可以看到原本灰色的控制点在拖拽时会亮起红色，为用户提供了良好的视觉提示。同时请注意，如果我们松开鼠标，最近被拖拽的那个控制点会保持红色。要修复这个问题，请在 `mouseUp:` 方法的末尾添加：

```
[self setNeedsDisplay:YES];
```




