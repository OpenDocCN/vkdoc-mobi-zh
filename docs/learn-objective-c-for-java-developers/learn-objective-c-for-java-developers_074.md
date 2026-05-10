# NIB 对象初始化

您通常希望执行一些额外的初始化。当 NIB 文档中的所有对象都创建完毕后，每个对象如果实现了 `-awakeFromNib` 方法，都会收到一条 `-awakeFromNib` 消息。`-awakeFromNib` 消息在所有 NIB 对象创建完成、所有连接和绑定都设置好之后发送。

请花点时间消化一下目前所介绍的内容。您已经了解了模型-视图-控制器设计模式的原则，以及一些流行的变体。您学习了数据模型、视图和控制器对象的基本通信职责。您还了解了绑定——这是一种非常具体的 MVC 通信实现。通过使用 Interface Builder，您学会了在应用程序中实例化对象，并建立它们相互通信所需的连接。剩下的唯一事情就是实现这些角色的实际类了。

接下来的几节将考察每一个 MVC 角色，并向您介绍用于实现自己角色所需的基本 Objective-C 和 Cocoa 技术。视图部分描述了窗口及其内容的视觉层次结构、视图对象的工作方式、图形坐标系以及可供您使用的基本绘图工具。后续章节将描述数据模型对象以及用于创建它们的数据建模工具，接着是一些对绑定有用的预构建控制器对象。其间还会详细描述用户事件如何到达视图对象，以及视图对象如何将产生的动作传递给它们的控制器。

### 视图

Cocoa 中视图对象的组织方式与 Java Swing 和其他 GUI 类库惊人地相似。这并不奇怪，因为基于窗口的用户界面中可视元素和控件的逻辑组织在所有现代平台上都相当一致。

表 20-1 列出了主要的 Swing 类及其对应的 Cocoa 类。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 20 章 ■ 模型-视图-控制器模式

**表 20-1.** 基础 Java 和 Objective-C 视图类

| Java (javax.swing) | Cocoa |
|---|---|
| `JWindow` | `NSWindow` |
| `JComponent` | `NSView` |
| (AbstractButton) | `NSControl` |
| `JButton` | `NSButton` (style=`NSMomentaryPushInButton`) |
| `JComboBox` | `NSComboBox` |
| `JPopupMenu` | `NSPopUpButton` |
| `JCheckBox` | `NSButton` (style=`NSSwitchButton`) |
| `JRadioButton` | `NSButton` (style=`NSRadioButton`) |
| `JSpinner.DateEditor` | `NSDatePicker` |
| `JSlider` | `NSSlider` |
| `JLabel` | `NSTextField` |
| `JTextArea` | `NSTextField` |
| `JTextField` | `NSTextField` |
| `JEditorPane` | `NSTextView` |
| `JPasswordField` | `NSSecureTextField` |
| `JProgressBar` | `NSProgressIndicator` |
| `JList` | `NSTableButton` |
| `JTable` | `NSTableView` |
| `JTree` | `NSOutlineView`, `NSBrowser` |
| `JScrollPane` | `NSScrollView` |
| `JSplitPane` | `NSSplitView` |
| `JTabbedPane` | `NSTabView` |
| `JToolbar` | `NSToolbar` |

[www.it-ebooks.info](http://www.it-ebooks.info/)

在使用 Cocoa 类时，请注意这些关键差异：`NSWindow` 不是 `NSView` 的子类，它本身也不是视图对象。相反，`NSWindow` 有一个 `contentView` 属性，该属性是窗口视图层次结构的根节点。要向窗口添加子视图，请将它们添加到 `window.contentView`。这个 `contentView` 最初是一个通用的 `NSView` 对象，仅充当子视图的容器，但您可以将其替换为任何 `NSView` 子类。

`NSView` 的尺寸由两个矩形属性描述：`frame` 和 `bounds`。`frame` 属性描述视图在其父视图中的位置和大小，并用父视图的坐标系表示。因此，`NSView` 的 `frame` 在概念上等同于 `JComponent` 的 `bounds`。`NSView` 的 `bounds` 属性是其内容的逻辑尺寸，用视图相对坐标表示，通常原点为 `(0,0)`。如果您没有设置自定义的 `bounds`，它将反映 `frame` 在本地坐标中的情况。如果您将 `bounds` 设置为不同的值，它就定义了一个独立于 `frame` 的本地坐标系（即 `bounds`）。

与 Swing 不同，所有发送动作事件的 Cocoa 视图类（即所有“执行操作”的视图对象——按钮、文本字段、滑块等）都是 `NSControl` 的子类。`NSControl` 在概念上类似于 `AbstractButton`，但它是所有 Cocoa 控件对象的基类，而不仅仅是按钮。`NSControl` 为其所有子类定义了四个通用概念：

-   控件有一个 `cell` 属性，它是一个 `NSCell` 对象。该 cell 负责控件的所有绘制和用户交互；`NSControl` 对象本身不执行任何绘制。实际上，`NSCell` 是控件的视图对象，而 `NSControl` 是控制器对象。对于每个 `NSControl` 子类的实例，都有一个默认的 `NSCell` 子类：`NSButton` 使用 `NSButtonCell`，`NSSlider` 使用 `NSSliderCell`，等等。要自定义任何 `NSControl` 的外观，请将其 `NSCell` 替换为您自己的自定义子类。
-   控件发送动作消息。`NSControl` 定义了 `action` 和 `target` 属性，这些属性可以通过编程方式或在 Interface Builder 中设置。通常，控件在其被激活或值改变时发送动作消息。
-   控件有一个 `objectValue` 属性，用于定义其内容。这并不适用于每个 `NSControl` 子类，但对于表示或显示值的控件来说，这是一个代表其内容的对象。对于文本字段，它是一个普通或格式化的字符串，或者任何可以转换为字符串的内容。对于图像视图，它将是一个图像对象。便捷属性 `stringValue`、`integerValue`、`doubleValue` 和 `floatValue` 用于获取或设置 `objectValue` 属性。
-   `NSControl` 定义了一个 `enabled` 属性，所有子类都应遵循该属性。`NSControl` 还定义了一组通用的编辑、验证和格式化行为，这些行为适用于某些子类。

要将 `NSView` 从其父视图中移除，请向该 `NSView` 对象发送 `-removeFromSuperview` 消息，或者如果您想抑制自动重绘，则发送 `-removeFromSuperviewWithoutNeedingDisplay` 消息。这与 Java 不同，在 Java 中，您必须告诉父视图移除特定的组件。



### NSView 不直接使用布局管理器

`NSView` 不使用布局管理器。相反，每个 `NSView` 对象都有一组调整大小标志（resizing flags），用于控制其子视图如何重新定位。这些标志可以在 Interface Builder 中轻松配置。您可以通过将 `autoresizesSubviews` 属性设置为 `NO` 来禁用此功能。您可以通过以下三种方式之一自定义自动调整大小行为：重写父视图的 `-resizeSubviewsWithOldSize:` 方法来重新定义其子视图如何调整大小；重写子视图的 `-resizeWithOldSuperviewSize:` 方法来重新定义其自身如何调整大小；或者将 `postsBoundsChangedNotifications` 属性设置为 `YES`，并监听 `NSViewBoundsDidChangeNotification` 通知。另请参阅本章后面的“动画”一节。此外，还有一些视图类可以实现与使用布局管理器几乎相同的效果；`NSMatrix` 提供了与 `java.awt.GridLayout` 基本相同的功能。

`NSView` 定义了一个 `tag` 属性——用于识别单个子视图——但该属性是不可变的，并且在基类中始终返回 0。`NSControl` 和其他一些子类重写了 `tag`，使其成为可变属性。如果您想创建 `NSView` 的自定义子类并为其分配一个标签（tag），您必须重写 `tag` 属性，使其可变，并提供一个存储该值的地方。

`NSTextField` 是一个多功能类，用于在窗口中显示大多数文本。它可以配置为显示不可变文本（标签）、显示和编辑单行文本（文本字段），或显示和编辑多行文本（文本区域）。它实现了一组丰富的属性，用于确定文本的显示、对齐、滚动和换行方式。

**提示：** 在创建您自己的 `NSView` 子类时，请在 Xcode 中使用 Objective-C `NSView` 子类源文件模板。它会创建一个子类，其中包含常用重写 `NSView` 方法的桩实现。

现在您已经了解了视图类的基本组织方式，您将希望开始向视图中填充子视图，甚至可能创建自己的自定义视图类。为此，您需要了解 Cocoa 绘图环境以及自定义 `NSView` 对象的工作原理，这些内容将在接下来的几节中描述。

### 视图几何（View Geometry）

Cocoa 的绘图几何与 Swing 略有不同。Cocoa 的自然坐标系与 Java 使用的坐标系是颠倒的，但可以选择翻转，使其与 Java 相同。笔的位置和绘图边界也不同。

在 Cocoa 中，所有图形值都是浮点数。这包括坐标、大小、宽度和颜色值。像红色或透明度这样的值通常表示为 0.0 到 1.0（含）之间的范围。这使得所有绘图原语独立于分辨率、显示设备和介质。

#### 坐标点（Coordinate Points）

Cocoa 中的坐标是连续坐标系中的抽象点。将坐标网格视为平面上的无限细线。大多数情况下，坐标与填充线条之间空间的像素是一一对应的（参见本节后面的图 20-11 和 20-12）。

从坐标的角度思考有助于避免“端点焦虑症”——即由绘图坐标与实际将绘制的像素之间的差异引起的焦虑。Cocoa 绘图始终发生在逻辑坐标之间。一条从坐标 0.0 到 10.0 的线将绘制一条恰好 10 个坐标空间长的线。填充一个 10×10 的矩形将填充恰好 100 个坐标单位的像素。很少有例外。一个值得注意的例外是贝塞尔曲线（Bezier lines），它们可能具有非正方形的线端帽，这可能导致其绘制超出线的终点。

所有绘图都是抗锯齿的，因此绘图很容易部分影响像素。

#### 坐标系（Coordinate System）

Cocoa 的自然坐标系是笛卡尔坐标系，它将 X，Y 原点放置在视图的左下角，如图 20-10 所示。

**图 20-10.** Java 和原生 Cocoa 坐标系

这是 Cocoa 的自然坐标系，但并非唯一可用的坐标系。`NSView` 定义了一个只读的 `flipped` 属性，图形系统会查询该属性。如果 `NSView` 子类在收到 `-isFlipped` 消息时返回 `YES`，Cocoa 将“翻转”视图的 Y 轴，创建一个与 Java 相同的坐标系。基础的 `NSView` 类、绘制图像的视图类以及作为容器的视图（标签视图、分割视图、滚动视图）都使用自然的笛卡尔坐标系。内容“从上到下流动”的视图对象（表格、大纲、列表、浏览器和文本视图）使用翻转的坐标系。在创建您自己的 `NSView` 子类时，您可以自由地重写 `isFlipped`，并选择最适合您内容的坐标系。`flipped` 属性被认为是视图的不可变属性；自发地更改它可能会导致不可预测的结果。

**注意：** 翻转坐标系会改变其子视图的框架矩形（frame rectangles）的含义。在处理框架矩形时，您的代码可能需要考虑父视图的坐标系是否被翻转。

除了翻转坐标系之外，`NSView` 的框架或内容可以被任意旋转和重新定位。通过设置 `frameRotation` 属性来旋转视图在其父视图中的位置，但不会改变视图内容的本地坐标系。设置 `boundsRotation` 属性会更改其内容的方向，但不会改变其在父视图中的位置。将 `boundsOrigin` 属性设置为 `(0,0)` 以外的值会移动视图在绘图时使用的坐标系。

`NSView` 提供了一系列方法用于在不同坐标系之间进行转换。主要的方法是 `-[NSView convertPoint:toView:]` 和 `-[NSView convertPoint:fromView:]`。这些方法接受视图本地坐标系中或另一个视图坐标系中的坐标，并将其转换为另一个坐标系中的坐标。另一个视图可以是窗口视图层次结构中的任何视图。如果视图对象参数为 `nil`，则它会转换为窗口的基本坐标系或从窗口的基本坐标系进行转换。还有一些方法可以在具有任意原点的坐标系之间转换坐标，以及一组转换矩形和大小结构的正交方法。

**提示：** 如果您需要在窗口之间转换坐标，首先将点转换为窗口的基本坐标系，然后使用 `-[NSWindow convertBaseToScreen:]` 方法将其转换为全局屏幕坐标。全局屏幕坐标可以使用 `-[NSWindow convertScreenToBase:]` 转换为本地窗口坐标，然后可以将其转换为任何子视图的本地坐标。

与 Java 一样，Cocoa 在逻辑坐标和物理屏幕像素之间保持区分。大多数情况下，可以忽略这种区别，因为坐标空间中的一个单位通常对应于屏幕上的一个像素。然而，不断变化的屏幕分辨率、辅助技术和手持设备正促使开发者认真对待与分辨率无关的绘图。一旦您熟悉了基本的绘图工具和技术，我建议您阅读《分辨率无关性指南》（Resolution Independence Guidelines）。

#### 笔的方向（Pen Orientation）

Swing 和 Cocoa 中的线都是使用一个虚拟的笔绘制的，该笔通过将笔的形状从一组坐标“拖”到另一组坐标来更改像素。在 Java 中，笔在坐标的右下方延伸。在 Cocoa 中，笔是无限细的，并且以逻辑坐标定义的线为中心。图 20-11 中显示的线是从坐标 (0，0) 到 (0，5) 使用 3 像素宽的笔绘制的（在 Java 中则是 3×3 像素的笔）。



***图 20-11.** Swing 与 Cocoa 的画笔方向*

该图展示了 Cocoa 画笔绘制了一条跨越四个像素列的抗锯齿线条。这是因为宽度为 3.0 像素的画笔以 Y 轴为中心，因此画笔“刷头”的左边缘位于 X 坐标-1.5 处，右边缘位于 1.5 处。要绘制一条模拟 Java 所绘制的 3 像素宽线条，Cocoa 线条需要从坐标(1.5,0.0)绘制到(1.5,8.0)。

3 Apple Inc., *分辨率无关性指南*, http://developer.apple.com/documentation/UserExperience/Conceptual/HiDPIOverview/, 2007。

[www.it-ebooks.info](http://www.it-ebooks.info/)

