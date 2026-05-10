# Tab View 与 App Delegate 的实现

Tab view（或任何其他`NSView`子类）放置在 nib 文件的顶层时，加载 nib 时不会显示，但只要我们有一个指向它的输出口，就能以任何方式访问和使用它，包括将其放入窗口的视图层次结构中。在我们的案例中，我们永远不会显示 tab view 本身，只会显示它包含的内容视图。为了获取这些内容视图，我们需要将其连接到 App Delegate 类。从 Object Dock 中的 tab view 按住 Control 键拖动到 Assistant Editor 中的`FIAppDelegate.h`文件，并创建一个名为`tabView`的输出口，以便后续访问。

双击 Object Dock 中的 tab view 条目，可以看到 tab view 出现在没有窗口环绕的界面中，如图 15-8 中 Interface Builder 画布的底部所示。它仍然带有调整大小的边框，我们可以在 Interface Builder 中像调整窗口一样调整它的大小。现在调整它的大小，使其与不久前我们放入主窗口的`NSBox`大致相同。

现在让我们向这个 tab view 添加一些内容，即我们可以在其间切换的“页面”。默认情况下，tab view 只包含两个内容视图，但我们增加这个数量（使用 Attributes Inspector），以便有更多视图可供切换。只要每个页面都有独特的内容以便能轻松看出页面之间的变化，实际内容并不那么重要。一个好的开始是从 Object Library 中抓取一个 Label，给它设置一个较大的字体，并将其文本改为单词“One”。然后复制这个标签并粘贴到其他每个视图中（我们可以像往常一样使用顶部的标签页在视图之间切换），每次相应地更改文本。为了有趣，还可以为每个页面添加一些独特的项目（这里的表格视图，那里的一组按钮），这样当应用完成时，在页面间切换时会看到更多动态效果。

现在是完善控制器类界面的时候了，我们将注意力转向`FIAppDelegate.h`文件。它目前声明了三个属性。第一个连接到主窗口。另外两个是我们为需要管理的两个视图添加的：`tabView`（包含我们要显示的视图的对象）和`box`（我们将显示这些视图的屏幕上的视图）。我们需要添加五个额外的属性。其中三个是`weak`属性，用于指向正在主动切换进出的视图。我们还需要添加一个`strong`属性来引用 tab view 包含所有可用视图的数组，以及一个`NSInteger`标量属性来标识当前聚焦的视图。

将粗体行添加到`FIAppDelegate.h`中，以添加所有这些内容。

```objc
@interface FIAppDelegate : NSObject <NSApplicationDelegate>

@property (assign) IBOutlet NSWindow *window;
@property (weak) IBOutlet NSBox *box;
@property (weak) IBOutlet NSTabView *tabView;
@property (weak) NSView *leftView;
@property (weak) NSView *rightView;
@property (weak) NSView *middleView;
@property (strong) NSArray *items;
@property NSInteger currentTabIndex;

- (IBAction)previous:(id)sender;
- (IBAction)next:(id)sender;

@end
```

现在让我们开始在`FIAppDelegate.m`中实现我们的 app delegate 类。这个类将包含多个方法，用于准备转换（通过将要显示的下一个视图设置在 box 的侧面），将新视图转换到位置，以及将当前视图转换到另一侧。因为我们需要根据是向右还是向左翻转来支持两个方向的转换，所以每个方法都存在两种形式：设置并执行向右翻转或向左翻转。此外，我们将实现两个启动转换的操作方法，以及一个设置初始视图的启动方法（`applicationDidFinishLaunching:`）。如果你仍然同时打开了 Interface Builder 画布和 Assistant Editor，你可能想关闭它们，并将`FIAppDelegate.m`切换为 Xcode 中的主视图。

让我们首先创建一个预处理器定义`ANIM_DURATION`，用于定义我们将创建的动画的持续时间（以秒为单位）。通过将其放在文件顶部的一个位置，我们可以轻松地进行实验，调整这个设置直到找到我们喜欢的速度。像这样定义它：

```objc
#define ANIM_DURATION 1.0
```

现在让我们转到`applicationDidFinishLaunching:`方法。这里我们从`tabView`获取视图列表，并将其存储在我们的`items`属性中。`items`属性需要是`strong`引用，因为`tabView`返回的是其内部数组的副本，而不是对数组本身的引用。这意味着我们的代码现在拥有由`tabViewItems`调用返回的`NSArray`的所有权。如果我们自己不将其放入`strong`引用中，那么数组副本将没有任何强引用，当自动引用计数清理时它将被释放。

我们还将`currentTabIndex`设置为指向数组的末尾，以便第一个项目能被对齐（更多细节将在后面说明）。然后我们调用第一个内部方法`prepareRightSide`，它将为显示在 box 右侧的下一个视图做准备。接着我们使用`ANIM_DURATION`值来指定当前动画上下文中创建的任何动画的持续时间。然后我们调用另一个内部方法`transitionInFromRight`，它将启动动画将下一个视图移动到正确位置。最后，我们将`currentTabIndex`设置为指向项目 0（`items`数组中的第一个对象）。

```objc
- (void)applicationDidFinishLaunching:(NSNotification *)aNotification {
    self.items = [self.tabView tabViewItems];
    self.currentTabIndex = [self.items count]-1;
    [self prepareRightSide];
    [[NSAnimationContext currentContext] setDuration:ANIM_DURATION];
    [self transitionInFromRight];
    self.currentTabIndex = 0;
    self.middleView = self.rightView;
}
```

当您输入这段代码时，Xcode 会抱怨找不到名为`prepareRightSide`和`transitionInFromRight`的方法。那么，现在让我们编写这两个内部方法的代码。我们不需要将这些方法放入单独的协议或其他东西中。在 Objective-C 中，代码可以自由调用同一`@implementation`块中声明的任何其他方法，即使这些方法没有在任何`@interface`中声明，所以我们只需将这些内部方法放在`@implementation`块中的某个位置——`applicationDidFinishLaunching:`的实现下方就是一个好位置。

第一个方法`prepareRightSide`首先确定要显示的下一个视图的索引。我们通过将`currentTabIndex`加 1 然后对新索引进行简单的边界检查来计算这个索引，如果索引过大则将其重置为零。然后我们使用该索引获取下一个视图，并将其帧大小设置为与`box`相同，但向右偏移正好`box`的宽度，使其刚好在视野之外。我们将其`alpha`值设置为`0.0`，使其实际上不可见，最后我们将该视图添加为`box`的子视图，这样它就能被实际显示出来。


```objc
- (void)prepareRightSide {

    NSInteger nextTabIndex = self.currentTabIndex + 1;

    if (nextTabIndex >= [self.items count])

        nextTabIndex = 0;

    self.rightView = [[self.items objectAtIndex:nextTabIndex] view];

    NSRect viewFrame = [self.box bounds];

    viewFrame.origin.x += viewFrame.size.width;

    [self.rightView setFrame:viewFrame];

    [self.rightView setAlphaValue:0.0];

    [self.box addSubview:self.rightView];

}
```

下一个方法 `transitionInFromRight` 接收 `rightView` 并将其滑入指定位置，使其完美适配 `box` 提供的空间。同时，它还将 alpha 值设置为 1.0，使其完全不透明。请注意，与上一个方法不同，这里使用了 `rightView` 的 `animator` 方法来访问视图的动画代理，这样设置这些值时就会自动为我们创建隐式动画。

```objc
- (void)transitionInFromRight {

    [[self.rightView animator] setFrame:[self.box bounds]];

    [[self.rightView animator] setAlphaValue:1.0];

}
```

在继续之前，我们先检查一下。运行应用，会看到标签页视图的第一个条目滑入到位，同时从不可见渐变到完全不透明，如图 15-9 所示。

![A978-1-4302-4543-8_15_Fig9_HTML.jpg](img/A978-1-4302-4543-8_15_Fig9_HTML.jpg)

**图 15-9.** 第一个“页面”正在滑入视图。注意盒子中的对象呈现略微灰白的外观，此时它们的不透明度大约为 50%

这是一个好的开始！现在让我们继续处理列表中的下一个条目：为 `next:` 方法提供实现。这段代码与我们在 `applicationDidFinishLaunching:` 方法中的代码有些相似。我们准备右侧视图，启动一些过渡动画（包括调用另一个新的内部方法 `transitionOutToLeft`，我们稍后会讲到），并在最后更新索引（包括另一次边界检查）和一些指针。

这里最大的区别在于，执行动画的方法都包裹在 `[NSAnimationContext beginGrouping]` 和 `[NSAnimationContext endGrouping]` 调用之间，这两个调用共同形成了一种事务机制。在这两个调用之间，任何添加到默认动画上下文中的动画（包括所有隐式动画）都将被设置为同时运行。这意味着当我们在内部方法中创建隐式动画时，它们都会被设置为同时触发。如果没有这一步，我们创建的动画将按顺序依次运行，一个接一个地执行。通常情况下这不会有太大问题，但完全有可能在创建这些动画时发生意外事件，例如其他进程突然占用 CPU，导致这些动画以略微错开的方式运行，开始和结束时间不同。通过像这样将它们包裹在分组中，就消除了这个潜在问题。

```objc
- (IBAction)next:(id)sender {

    [self prepareRightSide];

    [NSAnimationContext beginGrouping];

    [[NSAnimationContext currentContext] setDuration:ANIM_DURATION];

    [self transitionInFromRight];

    [self transitionOutToLeft];

    [NSAnimationContext endGrouping];

    self.currentTabIndex++;

    if (self.currentTabIndex >= [self.items count])

        self.currentTabIndex = 0;

    self.leftView = self.middleView;

    self.middleView = self.rightView;

}
```

`next:` 方法还调用了另一个内部方法 `transitionOutToLeft`，该方法会将当前视图向左移出。其实现如下：

```objc
- (void)transitionOutToLeft {

    NSRect newFrame = [self.middleView frame];

    newFrame.origin.x -= newFrame.size.width;

    [[self.middleView animator] setFrame:newFrame];

    [[self.middleView animator] setAlphaValue:0.0];

}
```

完成这些之后，我们现在可以再次运行了。这次我们不仅能看到初始视图设置正常运作，还能点击“下一个”按钮过渡到下一个视图！非常流畅。见图 15-10。

![A978-1-4302-4543-8_15_Fig10_HTML.jpg](img/A978-1-4302-4543-8_15_Fig10_HTML.jpg)

**图 15-10.** 视图三正在退出，视图四已进入约一半

现在剩下的工作就是实现向右过渡的对应方法。这些方法与之前的非常相似，在此不做进一步说明，但有一点要提醒：你可能会想复制粘贴现有方法并修改你能发现的不同之处，但请务必小心！有些差异虽然细微却至关重要。

```objc
- (void)prepareLeftSide {

    NSInteger previousTabIndex = self.currentTabIndex-1;

    if (previousTabIndex < 0)

        previousTabIndex = [self.items count]-1;

    self.leftView = [[self.items objectAtIndex:previousTabIndex] view];

    NSRect viewFrame = [self.box bounds];

    viewFrame.origin.x -= viewFrame.size.width;

    [self.leftView setFrame:viewFrame];

    [self.leftView setAlphaValue:0.0];

    [self.box addSubview:self.leftView];

}

- (void)transitionInFromLeft {

    [[self.leftView animator] setFrame:[self.box bounds]];

    [[self.leftView animator] setAlphaValue:1.0];

}

- (void)transitionOutToRight {

    NSRect newFrame = [self.middleView frame];

    newFrame.origin.x += [self.box bounds].size.width;

    [[self.middleView animator] setFrame:newFrame];

    [[self.middleView animator] setAlphaValue:0.0];

}

- (IBAction)previous:(id)sender {

    [self prepareLeftSide];

    [NSAnimationContext beginGrouping];

    [[NSAnimationContext currentContext] setDuration:ANIM_DURATION];

    [self transitionInFromLeft];

    [self transitionOutToRight];

    [NSAnimationContext endGrouping];

    self.currentTabIndex--;

    if (self.currentTabIndex < 0)

        self.currentTabIndex = [self.items count]-1;

    self.rightView = self.middleView;

    self.middleView = self.leftView;

}
```

现在点击运行；我们应该能够看到两个方向的动画过渡了。

## 总结

希望上一章和本章为你提供了多种 Cocoa 绘图技术的坚实基础，包括贝塞尔曲线的多种用法、通过鼠标使视图具有交互性，以及使用 Core Animation 实现轻松动画。由于本书篇幅所限，我们无法深入探讨这些主题，但请记住，尤其是在图形和动画方面，唯一的限制就是你的想象力！我们提供了基础工具。如果你想在图形方面做更多尝试，应该深入探索你最感兴趣的领域，看看能用 Cocoa 提供的 API 做些什么。这才是真正的乐趣所在！


