# Cocoa 框架与响应者链

Cocoa 框架通过检查响应者链中的对象来动态确定菜单项的状态。每个菜单项都与一个动作相关联。当用户下拉菜单时，框架会搜索响应者链，以找到能响应菜单中每个项的对象。如果链中存在能响应某个动作的对象，则启用该菜单项；否则，将其禁用。

**注意：** 您可以使用 `-[NSApplication targetForAction:to:from:]` 自行搜索响应者链中的对象。

这个系统的妙处在于，视图和控制器无需花费任何时间或精力来决定哪些菜单项应该启用。菜单项的状态是根据响应者链中对象的集合，通过经验方法自动确定的。

要在应用程序中实现一个执行特定功能并能自动启用和禁用的菜单命令，请执行以下步骤：

1.  在负责实现命令的对象中，创建一个动作方法：

    ```
    -(IBAction)action:(id)sender
    ```

2.  在 Interface Builder 中创建一个菜单项，并将其动作设置为步骤 1 中的消息标识符，**连接到第一响应者**。

仅此而已。这就是您需要做的全部工作。菜单项发送的动作将自动连接到响应者链中的适当对象，否则该菜单项将被禁用。

在 TicTacToe 项目中，`TTTDocument` 对象实现了两个动作：`-reset:` 和 `-playForPlayer:`。菜单项 *Reset* 和 *Play One Move* 会发送这些动作。此外，还有一个 *Reset* 按钮也会发送相同的动作。打开和关闭 TicTacToe 文档窗口，观察这对菜单项状态的影响。

## 禁用动作菜单项

虽然根据响应者链中的对象自动调整菜单项外观的功能很酷，但有些情况下，响应者链中的某个对象实现了某个动作，但该动作并非总是不合时宜。Cocoa 框架再次利用响应者链，让实现这一目标变得简单。

在前一节中，我遗漏了一个关于菜单项的步骤。一旦定位到菜单项的响应者，菜单框架会检查它是否实现了 `-validateMenuItem:` 方法。如果实现了，框架会通过发送该消息，并将待检查的菜单项作为参数传递，从而给该对象一个禁用菜单项的机会。如果对象返回 `YES`，则启用菜单项。否则，菜单项将被禁用，效果如同该对象未响应此动作。

在 TicTacToe 项目中，*Reset* 和 *Play One Move* 这两个菜单项都会向响应者链发送动作。如果不做任何其他处理，只要文档对象在响应者链中，两个菜单项都将处于启用状态。但是，游戏的复位命令只有在游戏开始后才有意义，而落子命令仅在轮到用户操作时才适用。清单 20-7 实现了一个 `-validateMenuItem:` 方法，用于处理这些情况。

**清单 20-7.** 菜单项验证方法

```objective-c
- (BOOL)validateMenuItem:(NSMenuItem*)menuItem
{
    if ([menuItem action]==@selector(reset:)) {
        return game.isStarted;
    } else if ([menuItem action]==@selector(playForPlayer:)) {
        return (game.nextPlayer==USER_PLAYER);
    }
    return [super validateMenuItem:menuItem];
}
```

清单 20-7 中的代码检查 `menuItem` 以确定正在验证哪个命令。然后，它根据游戏的当前状态决定是否应该启用该菜单项。这种实现方式通过菜单项发送的动作来识别它。另一种常用的技术是在 Interface Builder 中为菜单项分配唯一的标签值，以便于识别（例如，`if ([menuItem tag]==1001) …`）。您可以选择任何喜欢的识别方法。您的对象只会接收到它所响应的动作的 `-validateMenuItem:` 消息。

#### 使用响应者链进行设计

响应者链也用于其他一些杂项目的。例如，它用于转换错误对象（参见第 14 章）以及确定哪些服务可用（服务是一种插件架构，允许其他应用与您的应用交互）。

响应者链将深刻影响您如何以及具体在何处实现应用的功能。在设计 Cocoa 应用时，请牢记以下准则：在菜单命令适用时位于响应者链中的对象中实现菜单项动作，而在不适用时将该对象从链中移除。

通常，动作应在涵盖该动作领域的最根对象中实现：

-   读取或写入文档的命令应在文档对象中实现，而不是在窗口中。
-   改变数据模型状态的动作应在控制器或数据模型对象中实现，而不是在视图中。
-   视图应直接实现编辑动作，但最终的更改应作为离散动作发送给控制器或数据模型。

在层级结构中逻辑正确的对象中实现您的目标动作，即使这会导致功能重复（除了向另一个对象发送单个消息外什么都不做）。这比试图对抗响应者链的组织方式要容易得多。

至此，我们终于讲完了视图对象。您的应用的整个外观和感觉都是由视图对象实现的，因此它们承担了很多工作。接下来的几个部分将探讨数据模型对象，最后是控制器对象。

### 数据模型

数据模型本质上是任何持有应用数据的对象。数据采取的形式、如何使用以及如何呈现给用户，因应用而异，千差万别。在 Cocoa 应用中，有四种通用类型的数据模型对象：传统表和树数据源、集合控制器、Core Data 对象以及自定义类。

#### 传统表和树模型

与 Swing 类似，Cocoa 提供了将对象集合显示为表格和树的视图。这些视图可以与传统的、与 Swing 非常相似的数据模型提供者接口，也可以绑定到 `NSObjectController` 对象。等效的视图和数据源类列于表 20-7。

**表 20-7.** 集合视图类

| Swing                          | Cocoa                  |
| ------------------------------ | ---------------------- |
| `javax.swing.JTable`           | `NSTableView`          |
| `javax.swing.table.TableModel` | `NSTableDataSource`    |
| `javax.swing.table.TableCellRenderer` | `NSCell`          |
| `javax.swing.table.JTableHeader` | `NSTableHeaderView` |
| `javax.swing.table.TableColumn` | `NSTableColumn`      |
| `javax.swing.JTree`            | `NSOutlineView`, `NSBrowserView` |
| `javax.swing.tree.TreeModel`   | `NSOutlineViewDataSource` |
| `javax.swing.tree.TreeCellRenderer` | `NSCell`          |

如果您习惯于使用 Swing 中的表格和树，那么您会非常熟悉 Cocoa 的类。两者的架构惊人的相似：

-   `NSTableView` 实现数据集合的表格视图。
-   表格数据由一个符合 `NSTableDataSource` 非正式协议的 `dataSource` 对象提供。
-   单元格渲染器是 `NSCell` 的子类。
-   一个 `NSTableView` 拥有一系列 `NSTableColumn` 对象，这些对象定义了表格中每列的标题和单元格渲染器。
-   `NSOutlineView` 以分层视图显示数据树，父节点可以展开和折叠，子节点可以缩进。
-   大纲视图的数据由一个符合 `NSOutlineViewDataSource` 非正式协议的 `dataSource` 对象提供。
-   与 Java 不同，`NSOutlineView` 是 `NSTableView` 的子类，并继承其列和单元格行为。在 Swing 中，等效的树视图是一个单列的 `NSOutlineView`。其中一列被指定为树；视图的其余部分可以显示与每个节点关联的其他表格数据。Finder 中的列表视图就是大纲视图的一个例子。
-   `NSBrowserView` 是另一种树形查看器，它在不同的列中显示树的每一层。



