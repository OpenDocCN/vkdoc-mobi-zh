# LOLView

那么，`LOLView` 本身呢？首先，它将拥有一些属性，与应用委托的属性类似，用于存储来自用户的值。但在此情况下，每当某个值发生变化时，我们希望触发重新显示，以便 `LOLView` 能够重绘。因此，我们自行实现 setter 方法，而不是使用编译器生成的默认 setter。由于我们使用自定义 setter，但允许编译器生成 getter，因此必须将属性标记为 `nonatomic`。编译器生成的 getter 和 setter 包含同步代码，以确保这些方法永远不会返回未完全构造的对象。由于我们构建了自己的 setter，编译器无法提供这种保证，但在此应用中我们无需在意。按照指示将以下代码添加到 `LOLView.h` 和 `LOLView.m` 中：

```
// LOLView.h:
#import <Cocoa/Cocoa.h>
@interface LOLView : NSView
@property (strong, nonatomic) NSImage *image;
@property (copy, nonatomic) NSString *text;
@end

// LOLView.m:
#import "LOLView.h"
@implementation LOLView
- (id)initWithFrame:(NSRect)frame
{
    self = [super initWithFrame:frame];
    if (self) {
        // 此处添加初始化代码
    }
    return self;
}
- (void)drawRect:(NSRect)dirtyRect
{
    // 此处添加绘图代码
}
- (void)setImage:(NSImage *)i {
    if (![i isEqual:_image]) {
        _image = i;
        [self setNeedsDisplay:YES];
    }
}
- (void)setText:(NSString *)t {
    if (![t isEqual:_text]) {
        _text = [t copy];
        [self setNeedsDisplay:YES];
    }
}
```

在两个 setter 方法中，我们直接使用了属性生成的实例变量。默认情况下，实例变量的名称是属性名前加下划线，但你可以根据需要控制名称，也可以控制是否自动合成实例变量。在 `setText:` 方法中，我们对传入的 `NSString` 进行了显式拷贝。这样做是因为我们在属性声明中承诺了这一点（注意它标记为 `copy` 而非 `strong` 或 `weak`）。通常，在接收 `NSString` 这类对象时这是一个好做法，因为调用者可能实际传递给你一个可变字符串，可能会被其他代码修改。




### 绘制位图

现在，我们来进入`LOLView`类的“核心”部分：绘制图像并叠加一些文字。先从图像开始，只需在创建类时已经存在的`drawRect:`方法中添加几行代码，就能快速将图像复制到位：

```
- (void)drawRect:(NSRect)dirtyRect {

  NSRect srcImageRect = NSMakeRect(0, 0, [self.image size].width,

    [self.image size].height);

  [self.image drawAtPoint:[self bounds].origin fromRect:srcImageRect

    operation:NSCompositeCopy fraction:1.0];

}
```

这里我们首先创建了一个名为`srcImageRect`的矩形，其原点为`(0,0)`，大小与图像尺寸相同。然后，我们向图像自身发送一条消息，指示它绘制由`srcImageRect`指定的图像部分（此处为整张图像），并将其绘制到当前图形上下文中，位置位于视图边界矩形的原点。简而言之，整张图像被复制，且其左下角将精确地位于视图的左下角。这里使用的绘制方法还允许我们指定一个操作（该操作决定了源图像与目标图像中透明度的合并方式），以及一个介于`0.0`和`1.0`之间的整数，用作整张图像的整体 Alpha 级别。任何低于`1.0`的值都会使图像部分透明；一直降到`0.0`则会使图像完全不可见。

现在，运行应用程序，看看目前的成果。应用程序打开后，显示一个基本空白的窗口，底部有一个图像框和文本字段。在某处找一张适合 LOLcat 的图片（我们使用的是通过 Flickr 找到的美国国家档案馆的非版权图像），将其拖入图像框，效果应类似于图 14-11。

![A978-1-4302-4543-8_14_Fig11_HTML.jpg](img/A978-1-4302-4543-8_14_Fig11_HTML.jpg)

图 14-11.

这里可没什么 LOL 元素

嘿，这可不尽如人意！我们看到的只是拖入图片的左下角！要是能有办法看到整张图片就好了……

### 让它滚动起来

当然，办法是有的。Cocoa 中包含一个名为`NSScrollView`的类，它能帮上忙。通过将一个视图放入`NSScrollView`内部，我们就能获得水平和垂直滚动条，用户可以利用它们滑动视图。滚动视图处理了所有繁重的工作。放在其中的任何视图的绘制代码都完全无需修改！将`LOLView`放入`NSScrollView`出奇地简单。我们只需添加少量代码，并在 Interface Builder 中进行一些调整即可。

首先，在 Xcode 中打开 nib 文件，准备滚动视图本身。选中`LOLView`，然后从菜单栏中选择 Editor ➤ Embed in ➤ Scroll View。现在，我们的`LOLView`被包裹在了滚动视图中，但定位和大小稍有偏差。移动滚动视图，直到蓝色参考线在窗口左上角闪烁，然后使用右下角的调整控件稍微调整大小，直到蓝色参考线在该侧闪烁。视图应几乎填满窗口宽度，边缘留有常规边框，并向下延伸至在其他控件上方留出合适的边距（参见图 14-12）。

我们还可以免费获得滚动视图的一些实用的缩放行为，这在用户可以通过捏合进行缩放的多点触控触控板上很有用。既然这是免费的，我们就利用一下。选中滚动视图后，打开属性检查器。其中一个选项是“Magnification（缩放）”。勾选旁边的“Allow（允许）”复选框，剩下的事情就是代码部分了。

![A978-1-4302-4543-8_14_Fig12_HTML.jpg](img/A978-1-4302-4543-8_14_Fig12_HTML.jpg)

图 14-12.

为滚动调整后的 LOLmaker 窗口

我们需要添加的代码实际上是`setImage:`方法的又一部分。我们要做的是，每次设置新图像时都调整`LOLView`的大小，使视图的尺寸与图像的尺寸匹配。之后，当`LOLView`被封装在`NSScrollView`中时，滚动视图会注意到新的视图大小，并自动重新渲染包括滚动条在内的所有内容。新代码如下所示：

```
- (void)setImage:(NSImage *)i {

  if (![i isEqual:_image]) {

    if (i) {

      NSRect newImageFrame = NSMakeRect(0, 0, [i size].width,

        [i size].height);

      [self setFrame:newImageFrame];

    }

    _image = i;

    [self setNeedsDisplay:YES];

  }

}
```

保存所有工作，点击运行，滚动视图就完成了！拖入一张大图，注意它最初位于左下方，但滚动条已经出现，我们可以拖拽到任何想要的位置，如果硬件支持，还可以捏合缩放！如图 14-13 所示。

![A978-1-4302-4543-8_14_Fig13_HTML.jpg](img/A978-1-4302-4543-8_14_Fig13_HTML.jpg)

图 14-13.

滚动条：如此简单，连猫都会

### 绘制文本

好的，现在进入最后一步：绘制文本。在 LOLcats 社区中，传统做法是使用 Impact 字体的文本标题，白色文字配黑色阴影。Mountain Lion 系统已安装此字体，因此所有用户都应该拥有它。这应该是小菜一碟。唯一稍微有点棘手的是选择合适的字号。因为图像可能尺寸各异，我们需要动态选择字号，使标题能占据视图的适当部分，同时又不会延伸到边缘之外。我们将通过测试几种字号来实现：从`1`开始，每次将字号翻倍，直到文本的宽度会超出视图本身。然后，将字号稍微调小一点，再绘制文本。所有这些操作通过添加下面加粗显示的行来完成：

```
- (void)drawRect:(NSRect)dirtyRect {

  // Drawing code here.

  NSRect srcImageRect = NSMakeRect(0, 0, [self.image size].width,

    [self.image size].height);

  [self.image drawAtPoint:[self bounds].origin fromRect:srcImageRect

    operation:NSCompositeCopy fraction:1.0];

  if (self.text != nil && [self.text length] > 0) {

    NSPoint textLocation = NSMakePoint(0,0);

    NSShadow *textShadow = [[NSShadow alloc] init];

    [textShadow setShadowOffset:NSMakeSize(0,0)];

    [textShadow setShadowColor:[NSColor blackColor]];

    [textShadow setShadowBlurRadius:10];

    NSMutableDictionary *textAttributes =

      [NSMutableDictionary dictionaryWithObjectsAndKeys:

        [NSFont fontWithName:@"Impact" size:40], NSFontAttributeName,

        [NSColor whiteColor], NSForegroundColorAttributeName,

        textShadow, NSShadowAttributeName,

        nil];

    // 找出最佳字号

    CGFloat fontSize;

    NSSize testSize = NSMakeSize(0, 0);

    for(fontSize=1; testSize.width < [self.image size].width; fontSize*=2)

    {

      [textAttributes setObject:[NSFont fontWithName:@"Impact"

        size:fontSize]

        forKey:NSFontAttributeName];

      testSize = [self.text sizeWithAttributes:textAttributes];

    }

    [textAttributes setObject:[NSFont fontWithName:@"Impact"

      size:fontSize/4]

      forKey:NSFontAttributeName];

    // 绘制文本

    [self.text drawAtPoint:textLocation

      withAttributes:textAttributes];

  }

}
```

现在点击运行，将图像拖入图像框，然后输入一些文字。瞧！效果应该类似于图 14-14。

![A978-1-4302-4543-8_14_Fig14_HTML.jpg](img/A978-1-4302-4543-8_14_Fig14_HTML.jpg)

图 14-14.

我能开新闻发布会吗？



