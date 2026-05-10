# 单事件与多事件控件对象

单事件对象（如 `UIBarButtonItem`）只有一个目标属性。这类对象只能向单个目标发送单一操作。你可以通过分别设置 `target` 和 `action` 属性，以编程方式建立操作连接，如下所示：

```
barButtonItem.target = viewController
barButtonItem.action = "refresh:"
```

更复杂的控件对象包含多种事件，其中任何一个事件发生时均可配置为发送操作消息。`UISlider` 对象可在用户触摸控件（`.TouchDown`）、在控件框架外拖拽（`.TouchDragOutside`）、在框架外松开手指（`.TouchUpOutside`）、在框架内松开手指（`.TouchUpInside`）或滑块值发生变化（`.ValueChanged`）时发送操作消息。每个事件均由一个事件常量唯一标识（参见 `UIControlEvents`）。任何事件都可配置为向多个目标发送操作消息。

在 ColorModel 项目中，你已在 Interface Builder 中将滑块的 `Value Changed` 事件连接到视图控制器的 `changeHue:` 操作。以下代码创建了相同的连接：

```
newSlider.addTarget( viewController,
             action: "changeHue:",
   forControlEvents: .ValueChanged)
```

**提示** `UIControlEvents` 是一个位元集合。将多个常量进行组合（OR），即可将同一操作消息同时附加到多个事件上。

## 发送操作消息

至此，你或许不会惊讶地发现，操作也可以通过编程方式发送。若要发送操作，只需调用应用对象（`UIApplication.sharedApplication()`）的 `sendAction(_:,to:,from:,forEvent:)` 函数即可。

`UIControl` 的子类通过调用自身的 `sendAction(_:,to:,forEvent:)` 函数来发送操作。而该函数实际上会转而调用应用对象的 `sendAction(_:,to:,from:,forEvent:)`，并将自身作为 `from:` 参数传递。如果你正在创建自定义的 `UIControl` 对象，并需要发送控件的操作，这正是应当调用的函数。

**提示** 如果操作是响应 iOS 事件（第 4 章）而发送的，礼貌的做法是将 `UIEvent` 对象包含在 `forEvent:` 参数中。否则，请传递 `nil`。

你可以通过调用 `sendActionsForControlEvents(_:)` 方法，以编程方式让任何 `UIControl` 对象发送与一个或多个事件关联的操作。

在所有情况下——无论是通过编程方式发送操作，还是配置控件对象时——目标对象都可以设置为 `nil`。当目标为 `nil` 时，操作将不会发送给特定对象，而是沿着响应链传递，从第一响应者开始（参见第 4 章）。若要沿响应链向上发送任意消息，可使用如下代码：

```
UIApplication.sharedApplication().sendAction( "orderIceCream:",
                                          to: nil, /* 响应链 */
                                        from: self,
                                    forEvent: nil)
```

现在，你已经深入理解了 Interface Builder 的工作原理，以及对象如何被创建、配置和连接。你还掌握了与 Interface Builder 功能等效的大部分代码，因此可以像在 Shapely 应用中那样，通过编程方式创建、配置和连接对象。

忘掉这些吧。嗯，也不是真的忘掉——你将来可能会用到——但暂且将其搁置一旁。了解 Interface Builder 文件的工作原理以及等效代码固然很好。但使用 Interface Builder 的意义恰恰在于，你无需亲自动手写这些代码！现在，不是用代码去替代 Interface Builder，而是该用 Interface Builder 来替代手写代码了。

