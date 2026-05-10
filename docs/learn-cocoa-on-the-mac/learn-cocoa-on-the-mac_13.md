# 创建场馆窗口

现在，我们需要再创建一个用于显示和编辑 `MythicalVenues` 的窗口。制作这个窗口的过程与创建 Mythical Bands 窗口非常相似——需要一个窗口、一个表格视图、几个按钮以及一个 `NSArrayController`。由于真正具有神话色彩的场馆数量并不多，我们将省略此窗口中的搜索框。此外，由于我们已经做过一次类似的工作，这次可以加快进度。

首先，从对象库中拖出一个窗口放到 Interface Builder 画布上。在属性检查器中，将其标题设置为“Mythical Venues”。这个窗口比我们需要的尺寸稍大，因此可以将其垂直缩小到大约三分之二的高度，并适当收窄宽度。拖出一个表格视图，将其放置在屏幕的左上角，让蓝色参考线提示我们具体位置。将表格视图扩展到填满窗口，如图 9-1 所示。在表格视图的属性检查器中，将其内容模式更改为基于视图（view-based），并设置为只有一列。将这一列扩展到填满表格视图的宽度。

向窗口中拖出一个渐变按钮（Gradient button），将其放置在表格视图右下边缘的下方。这将作为我们的“添加”按钮，因此双击它并将标题改为“Add”。再拖出另一个按钮，将其放置在“Add”按钮的左侧，并将这个按钮的标题改为“Remove”。一如既往，蓝色参考线能帮助我们精确地摆放位置。

接下来，我们需要一个 `NSArrayController`。从对象库中抓取一个并将其拖到 Interface Builder 画布上。在左侧的对象停靠栏中，将其命名为“Mythical Venues”。在属性检查器中，将其模式设置为“Entity Name”，将实体名称设置为“MythicalVenue”，并勾选“Prepares Content”复选框，以便在加载 nib 文件时获取所有场馆。我们还需要将 Mythical Venues 数组控制器连接到 App Delegate 提供的托管对象上下文。在绑定检查器中，展开底部的 Parameters 部分。勾选“Bind to”复选框，将下拉菜单设置为“App Delegate”，并将模型键路径（Model Key Path）设置为“managedObjectContext”。

现在，我们需要将所有部分连接起来。先从表格视图开始。选择表格视图（记住它内嵌在滚动视图中）。在绑定检查器中，展开“Table Content”下的“Content”部分。然后，勾选“Bind to”复选框，并将下拉菜单设置为“Mythical Venues”。默认的控制器键 `arrangedObjects` 就可以。接下来处理列。就像我们之前对 Band Members 表格视图所做的操作一样，深入表格视图内部，找到“Static Text – Table View Cell”。在绑定检查器中，展开“Value”部分。下拉菜单应显示为“Table Cell View”。勾选“Bind to”复选框，将模型键路径（Model Key Path）设置为“objectValue.name”，同时将控制器键（Controller Key）字段留空。趁在此处，切换到属性检查器，将 Behavior 设置从“None”改为“Editable”，这样当我们创建新场馆时，就可以在表格视图内设置名称。

接下来，处理按钮。选择“Add”按钮，按住 Control 键拖拽到 Mythical Venues 数组控制器上。从弹出菜单中选择 `add:` 操作。然后，选择“Remove”按钮，按住 Control 键拖拽到同一个位置。这次，选择 `remove:` 操作。为了根据表格视图中的选择来启用或禁用按钮，我们需要使用绑定检查器。选择“Add”按钮，打开绑定检查器，展开“Availability”下的“Enabled”部分。勾选“Bind to”复选框，将下拉菜单设置为“Mythical Venues”，并将控制器键（Controller Key）字段改为“canAdd”。对于“Remove”按钮，执行相同操作，但控制器键字段使用“canRemove”。

再次保存我们的工作，在 Xcode 中点击运行，查看新功能：我们可以添加和删除新的 Mythical Venues。

