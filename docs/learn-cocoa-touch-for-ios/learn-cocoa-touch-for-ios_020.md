# 响应者（Responder）就是任何能响应用户输入的对象。由于 `UIView` 继承自 `UIResponder`，我们知道它可以接收用户输入。在 Cocoa Touch 中，用户的触摸是一种 `UIEvent`，会被传递给一个 `UIResponder` 对象进行处理。`UIEvent` 不仅仅是触摸事件：当用户摇动设备或使用远程控制（例如 Apple 品牌耳机的音量控制）时，也会生成这些事件。

当用户点击屏幕时，系统会找出该位置最上层的视图，并向其发送一个包含一个或多个 `UITouch` 对象的 `UIEvent` 对象。大多数情况下，用户一次只输入一个触摸，但偶尔（尤其是在屏幕更大的 iPad 上）可能会同时使用两根或多根手指进行触摸。一旦事件被发送到最上层的 `UIView` 对象，它就进入了所谓的响应者链。响应者链中的每个对象都负责要么处理该事件（从而消耗它），要么将其发送给链中的下一个对象。链中的每个对象都继承自 `UIResponder`，因此它们都拥有一套共同的评估触摸（一个或多个）的方法。`UIResponder` 还声明了 `nextResponder` 方法，该方法返回响应者链中的下一个响应者。

让我们跟随用户的触摸在响应者链中向上传递，看看它最终会到哪里。首先，用户点击屏幕，创建 `UITouch` 和 `UIEvent` 对象。系统分析屏幕上的当前视图层次结构，确定包含用户触摸坐标的最顶层视图，并将包含触摸和事件作为参数的消息发送给它（稍后我们将详细讨论这个消息的具体内容）。如果该视图不响应触摸，它会调用自身的 `nextResponder`。`UIView` 对 `nextResponder` 的实现是：如果视图有对应的视图控制器，则返回该视图控制器——`UIViewController` 也继承自 `UIResponder`。如果没有，则返回其父视图（即包含它的那个视图）。如果视图控制器没有消耗触摸，则在其 `nextResponder` 实现中返回该视图的父视图。因此，在视图层次结构深处多层的触摸可能会依次经过：一个视图、几层父视图、一个视图控制器，再返回到另一个视图，依此类推。最终，`UIView` 的 `nextResponder` 方法将返回任何应用程序中最顶层的视图，即 `UIWindow`。如果窗口没有消耗视图，它的下一个响应者就是代表前台应用程序的 `UIApplication` 对象。虽然可以通过子类化 `UIApplication` 来自定义事件处理，但这种做法非常少见，最好避免。相反，从 iOS 5 开始，你的应用程序委托可以从 `UIResponder` 而非 `NSObject` 继承，这为你提供了一个在应用程序级别响应事件的更好位置。

## 对于触摸事件，应用程序很容易确定用户想要点击哪个视图，因为它知道触摸的位置。但对于其他事件，就不那么清楚了。当用户摇动设备或点击屏幕键盘时，会生成一个事件，但没有明显的方法来确定哪个对象应该接收它。在 Cocoa Touch 中，这个答案被称为第一响应者（first responder）。当一个对象成为第一响应者时，它就成为了此类事件的起始点。实际上，要让屏幕键盘出现，你可以调用 `UITextField` 对象的 `becomeFirstResponder` 方法，也可以通过在该文本字段上调用 `resignFirstResponder` 来强制使其消失。

例如，如果你想实现一个在摇动设备时改变颜色的视图，第一响应者也会很有用。

## 自定义视图

处理用户输入最直接的方法就是创建你自己的 `UIView` 子类。虽然这是最直接的方法，但也是工作量最大的方法。

在 `UIResponder` 的子类中，你可以直接实现触摸方法。

对应于触摸的四个阶段，触摸方法如下：

```
- (void)touchesBegan:(NSSet *)touches withEvent:(UIEvent *)event;

- (void)touchesCancelled:(NSSet *)touches withEvent:(UIEvent *)event;

- (void)touchesEnded:(NSSet *)touches withEvent:(UIEvent *)event;

- (void)touchesMoved:(NSSet *)touches withEvent:(UIEvent *)event;
```

实现所有这四个方法非常重要，因为 UIKit 框架期望一个 `UIView` 能够处理完整的触摸事件集。每个方法共同拥有一个 `NSSet` 类型的触摸集合和一个它们所属的 `UIEvent` 对象。`NSSet` 是一个类似于 `NSArray` 的集合类，但它是无序的。集合和数组之间的一个重要区别是，一个对象在一个集合中只能出现一次，但在一个数组中可以出现在多个位置。尝试将一个已存在于集合中的对象再次添加到该集合将不起作用。还有 iOS 5 引入的 `NSOrderedSet` 类，它像数组一样有序，但又像集合一样强制对象唯一性。让我们看一个视图的实现示例。当用户点击视图时，想要调用 `didReceiveTap` 方法，你只需要在 `touchesEnded:withEvent:` 方法中添加代码，尽管你需要实现所有四个方法：

```
- (void)touchesBegan:(NSSet *)touches withEvent:(UIEvent *)event
{

}

- (void)touchesMoved:(NSSet *)touches withEvent:(UIEvent *)event
{

}

- (void)touchesEnded:(NSSet *)touches withEvent:(UIEvent *)event
{
    [self didReceiveTap];
}

- (void)touchesCancelled:(NSSet *)touches withEvent:(UIEvent *)event
{

}
```

此列表中的第四个方法 `touchesCancelled:withEvent:` 在系统中断触摸时被调用。例如，如果你正在创建一个绘画应用，用户在绘画时接到了电话，就会调用此方法。它与 `touchesEnded:withEvent:` 的关键区别在于，前者由系统触发，而后者由用户触发。

虽然实现点击很容易，但更高级的触摸模式需要大量的编码工作。要实现向右滑动，你必须跟踪所有触摸，测量它们之间的距离，并决定允许用户绘制多接近水平线的距离才能触发滑动手势。要实现捏合，你需要跟踪两条或更多条移动线。这些方法的一个常见问题是，由于你自己实现了所有代码，一个开发者的做法会与另一个不同。我的代码可能比你的更宽容，会把歪斜的滑动视为水平滑动，而你的代码可能允许比我的更慢的双击。更糟糕的是，我们都需要花费时间编写触摸处理代码！幸运的是，从 iOS 3.2 引入 iPad 开始，Apple 引入了 `UIGestureRecognizer` 类，它为触摸处理的难题提供了解决方案。

## UIGestureRecognizer

`UIGestureRecognizer` 是一个辅助对象，旨在消除触摸识别中的猜测工作和手动编码。有几个预制的子类可供使用，它们共同构成了一个强大的套件。你可以创建任意数量的手势识别器，甚至可以将多个手势识别器用于单个视图。你可以子类化 `UIGestureRecognizer`，以高效且可重用的方式实现自定义手势，甚至可以将它们与你自己的手动触摸处理代码混合使用。

### 更多 Target-Action 方法

就像我们之前看到的 `UIControl` 子类一样，`UIGestureRecognizer` 使用 Target-Action 范式进行操作。与 `UIControl` 不同，这里没有多个控件事件或要发送的不同动作；手势识别器只有在成功识别其手势时，才会对目标执行该动作。你可以使用 `addTarget:action:` 方法为单个手势识别器添加多个对象作为目标。

### 手势识别器的生命周期



手势识别器的创建与其他对象相同，通过 `initWithTarget:action:` 方法进行初始化。由于识别出手势却不做任何处理毫无意义，因此创建手势识别器时必须至少指定一个目标-动作对。创建完成后，使用 `UIView` 的 `addGestureRecognizer:` 方法将其添加到视图中。手势识别器在特定视图上工作，因此虽然不能将一个手势识别器添加到两个视图，但可以将其添加到它们共有的父视图上。

手势识别器添加到视图后，会经历一系列状态（可通过其 `state` 属性访问）。初始状态为 `possible`。手势结束时，状态为 `recognized` 或 `failed`，具体取决于接收到的触摸是否匹配该手势。如果状态为 `recognized`，手势识别器会调用其目标上的动作方法。

部分手势识别器是连续型的，这意味着它们不是检测单一离散手势，而是在接收触摸时持续发送更新。例如在绘图应用中追踪触摸时，可使用连续手势识别器在用户移动手指时即时获取更新。对于连续手势识别器，还存在附加状态：`began`（触摸开始时调用）、`changed`（每次触摸变化时调用）以及 `ended`（相当于非连续手势识别器的 `recognized` 状态）。

### 内置手势识别器

苹果公司在 iOS 5 中提供了六种内置手势识别器，它们都是 `UIGestureRecognizer` 的子类。按复杂度递增顺序排列如下：

- **`UITapGestureRecognizer`**：点击手势识别器用于识别点击操作。可通过修改 `numberOfTapsRequired` 属性来设置连击次数，以及 `numberOfTouchesRequired` 属性来设置多点触摸的手指数量。
- **`UILongPressGestureRecognizer`**：长按是一种不会立即释放的点击。长按手势识别器与 `UITapGestureRecognizer` 共享 `numberOfTapsRequired` 和 `numberOfTouchesRequired` 属性，同时还具有 `minimumPressDuration` 属性（可设置长按判定时间）和 `allowableMovement` 属性（定义触摸在取消长按前允许移动的最大距离）。长按手势识别器的优势在于这两个值已设置为合理的默认值，使所有使用它们的应用保持一致性。
- **`UISwipeGestureRecognizer`**：滑动手势识别器可识别用户向上、左、右或向下滑动，这些方向通过 `direction` 属性暴露，并允许指定 `numberOfTouchesRequired` 属性。滑动手势识别器不是连续型的，因此只有在用户完成滑动后才会接收到其消息。
- **`UIPanGestureRecognizer`**：平移手势识别器同样识别用户手指的移动，但以连续方式进行。可通过设置 `maximumNumberOfTouches` 和 `minimumNumberOfTouches` 属性自定义其行为，并有两个方法来查询其值：`translationInView:` 和 `velocityInView:`。例如，平移量可用于根据用户拖拽手指重新定位视图，而速度（以每秒点数衡量）则可用于实现惯性甩动视图等炫酷功能。
- **`UIPinchGestureRecognizer`**：捏合手势识别器测量手指朝向或远离所有触摸中心点的连续移动。它通过相对位置变化来调整其 `scale` 属性，例如可用于调整视图大小。捏合手势识别器还提供了 `velocity` 属性。
- **`UIRotationGestureRecognizer`**：与捏合手势识别器类似，旋转手势识别器测量两


