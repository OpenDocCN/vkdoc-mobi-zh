# 传统动画

还缺少的是告诉玩家他们的分数是什么。这将让你回到完整的原点，即直接使用动画序列。

当游戏结束时，创建一个显示分数的新标签节点。在 `endGame(_:)` 函数的末尾，使用以下代码执行此操作：

```
let score = SKLabelNode(text: "Score: \(score)")
score.fontName = "Helvetica"
score.fontColor = SKColor.greenColor()
score.fontSize = CGFloat( view!.traitCollection.horizontalSizeClass == .Compact
                                                 ? 54.0 : 120.0 )
let sceneRect = CGRect(origin: CGPointZero, size: frame.size)
score.position = CGPoint(x: sceneRect.midX, y: sceneRect.height*0.1)
score.zPosition = GamePlane.Score.rawValue
addChild(score)
```

用你学到的所有知识，这段代码应该很容易理解。代码创建一个新的 `SKLabelNode`，配置其字体和文本，并将其定位在场景的水平中心。请注意，它将节点的 `zPosition` 设置为 `GamePlane.Score`，因此它会绘制在所有其他精灵之上。最后，该节点被添加到场景中。

运行你的应用。赢得游戏或让它结束，你的分数就会出现。

这很好，但很无聊。

回到这段代码。通过将以下代码添加到你刚才写的代码中，将新标签精灵的初始缩放比例设置为较小值（添加的代码以粗体显示）：

```
score.zPosition = GamePlane.Score.rawValue
score.xScale = 0.2
score.yScale = 0.2
addChild(score)
```

现在创建动作来恢复标签的大小、淡入标签并向上移动标签——所有这些同时进行。紧接在前面的代码之后，添加以下内容：

```
let push = SKAction.moveToY(sceneRect.height*0.8, duration: 1.0)
push.timingMode = .EaseOut
let grow = SKAction.scaleTo(1.0, duration: 1.2)
grow.timingMode = .EaseIn
let appear = SKAction.fadeInWithDuration(0.8)
let drama = SKAction.group([push,grow,appear])
score.runAction(drama)
```

组动作是一种同时启动两个或多个动作的动作。因此，调整大小、移动和淡入效果同时开始。还要注意这些动作都有不同的持续时间。经过 1.0 秒后，标签已经完全显现并刚刚停止移动，但它仍在增长。组动作在其最后一个动作完成时结束。

现在运行你的游戏，看看它如何结束。是的，这样有趣多了。（遗憾的是，我无法在印刷品中向你展示效果。你必须自己运行应用。）

现在怎么办？你的游戏结束了，但无法重新开始。哎呀！老实说，大多数游戏并不是直接从游戏中开始的。它们通常有一个欢迎场景，你可以在其中开始游戏、访问帮助和设置等。让我们重新设计 `SpaceSalad`，使其有一个欢迎场景，你也会看到在 `SpriteKit` 视图中切换场景是多么容易。

