# 3. 为游戏添加物理与碰撞检测

James Goodwill¹ 和 Wesley Matlock²  
(1) 美国科罗拉多州海兰兹牧场  
(2) 美国密苏里州堪萨斯城

本章介绍 SpriteKit 的物理引擎和碰撞检测。我们首先讨论 `SKPhysicsBody`——用于模拟碰撞检测的类。然后你将在游戏世界中启用重力，并观察它如何影响节点。之后，你将添加一个触摸响应器来将 `playerNode` 推向空中。在本章结尾，我会讨论如何处理节点碰撞。

### 什么是 SKPhysicsBody？

为了在 SpriteKit 游戏中模拟物理，你需要向场景或节点添加一个物理体。物理体由 SpriteKit 类 `SKPhysicsBody` 实现，它是一个模拟对象，附加到场景节点树中的节点上。`SKPhysicsBody` 类使用节点的属性，如 `position` 和 `velocity`，并与其自身属性结合，来模拟物理力是如何作用到模拟游戏世界中的。它在渲染循环的每次迭代中进行计算来执行这些模拟。

有三种类型的物理体：动态体积、静态体积和边缘。

- **动态体积** 是一个具有体积和质量的物理体，会受物理模拟中的碰撞和力的影响。附着有动态体积的节点是游戏中的活跃节点。
- **静态体积** 物理体与动态体积类似，但力和碰撞对其没有影响。静态体积体通常用作游戏中的屏障。当动态体与静态体碰撞时，动态体会受到碰撞影响，而静态体保持不变。
- **边缘** 是一种没有体积的静态体。模拟永远不会移动边缘，且边缘的质量不会影响其他节点的模拟。边缘代表场景内的负空间。

### 为游戏世界添加物理



### 理解 SpriteKit 中的物理体

要理解这一切是如何运作的，最简单的方法就是直接动手实践。让我们先创建一个 `SKPhysicsBody` 并将其与 `playerNode` 关联起来。在 `GameScene` 的 `.init(size: CGSize)` 方法中，紧随 `playerNode` 构造代码之后，添加以下两行代码：

```
playerNode.physicsBody = SKPhysicsBody(circleOfRadius: playerNode.size.width / 2)
playerNode.physicsBody?.isDynamic = true
```

仔细看看这两行代码。第一行创建了一个 `SKPhysicsBody`，并向其初始化方法传入了一个名为 `circleOfRadius`、值为 `CGFloat` 类型的参数。`SpriteKit` 提供了几种标准的物理体形状，包括圆形（`circleOfRadius`）、矩形（`rectangleOfSize`）以及基于路径的多边形（`polygonFromPath`）。这些形状中，圆形的效率最高，而基于路径的多边形效率最低。由于圆形是最高效的节点形状，因此本游戏中的所有物理体都将使用它。

在这个例子中，传入构造函数的参数值是 `playerNode` 的宽度除以 2。我们使用这个值是因为，我们希望以 `playerNode` 的中心为圆心，以节点宽度的一半为半径，创建一个包围该节点的圆形。这样就能得到一个完全包裹 `playerNode` 的圆。如果存在 `SKPhysicsBody(circleOfDiameter:)` 这样的构造函数，我们就不需要将宽度除以 2 了，但并没有这样的构造函数。

现在来看这段代码的第二行。第二行将 `playerNode` 转变为一个具有动态体积的物理体。此后，它将响应场景中的重力以及其他物理体。完成这些修改后，你的新玩家设置代码应该如下所示：

```
// 添加玩家
playerNode.physicsBody = SKPhysicsBody(circleOfRadius: playerNode?.size.width / 2)
playerNode.physicsBody?.isDynamic = true
playerNode.position = CGPoint(x: size.width / 2.0, y: 80.0)
addChild(playerNode)
```

现在，回到 Xcode 并再次运行应用。如果你没有仔细观察，你只会看到游戏的背景节点。这是因为 `SpriteKit` 中的默认重力设置与地球引力一致，玩家节点现在正快速地向地心坠落。要想让下落速度变慢，你需要调整游戏世界的重力设置，直到找到一个你满意的重力值。

回到 `GameScene.init(size: CGSize)` 方法，在 `super.init(size: size)` 调用之后立即添加以下代码行，然后再次运行应用：

```
physicsWorld.gravity = CGVector(dx: 0.0, dy: -0.1)
```

现在你会看到玩家慢慢地从屏幕底部飘落。你刚才所做的是通过 `SKScene` 的 `physicsWorld.gravity` 属性修改了世界重力，并将其设置成一个能大幅减缓 `playerNode` 下落速度的值。请注意，你设置给重力属性的值是一个向量，其 x 坐标为 `0.0`，y 坐标为 `–0.1`。你将 x 坐标设为 `0.0`，是因为重力只沿 y 轴方向施加力。这个向量的结果是一个将物体向场景底部拉拽的重力。

虽然将 y 坐标设为 `–0.1` 有助于我们看到 `playerNode` 掉落出场景，但这对于游戏玩法来说并不实用。一个更合理的值应该是 `–2.0`。将重力属性设置为 `CGVector(dx: 0.0, dy: -2.0)`，然后再次尝试运行。你会看到玩家掉出屏幕，但速度更适合游戏玩法。

**注意：** 在本节中，你将世界的重力属性修改为了一个与地球实际重力不一致的值。我们之所以让你这样做，是因为你的目标不是模拟地球引力，而是创建一个让玩家感到有趣的模拟游戏世界。当你开始创建自己的游戏时，你会发现自己经常这样做。毕竟，游戏的核心是让玩家玩得开心，而不是与现实世界一致。

### 向 SKPhysicsBody 施加力



## 排版后的内容

此时，你的玩家已经能响应游戏世界的物理属性，并且你也调整了引力，防止玩家从可视场景底部掉落。这很棒，但看着角色掉出屏幕可没什么乐趣。现在，是时候向玩家施加一些力，让他能对抗重力并留在可视场景中了。

改变 `SKPhysicsBody` 速度的两种最常见方法是施加力（`applyForce()`）和施加冲量（`applyImpulse()`）。当向 `SKPhysicsBody` 施加力时，该力的作用时间基于渲染循环两次调用之间经过的时间长度。力通常用于连续的速度变化。你使用 `SKPhysicsBody` 的 `applyForce()` 方法来施加力。当向 `SKPhysicsBody` 施加冲量时，你是在对物体的速度施加一个瞬时改变，这个改变与经过的模拟时间无关。当你需要立即改变节点速度时，会使用冲量。你使用 `SKPhysicsBody` 的 `applyImpulse()` 方法来施加冲量。

本游戏将使用冲量来修改玩家的速度，因此会使用 `applyImpulse()` 方法。在你能向玩家的物理体施加冲量之前，你需要让用户能够告知游戏何时施加冲量。因为 `SKNode` 继承了 `UIResponder`，并且 `SKScene` 是一个 `SKNode`，你可以通过重写 `GameScene` 的 `UIResponder.touchesBegan()` 方法来测试场景中的触摸。为此，你需要完成两步。

首先，在创建 `backgroundNode` 的代码行之前，添加以下一行代码以开启场景中的用户交互：
`isUserInteractionEnabled = true`

然后，通过在 `GameScene` 类中添加以下方法来重写 `UIResponder.touchesBegan()`：
```
override func touchesBegan(_ touches: Set<UITouch>, with event: UIEvent?) {
    playerNode.physicsBody?.applyImpulse(CGVector(dx: 0.0, dy: 40.0))
}
```

将此方法添加到 `GameScene` 类定义的底部，然后重新运行 `SuperSpaceMan` 应用程序。在应用运行时，根据需要多次点击屏幕，让玩家飞回场景中。所需的点击次数取决于 `playerNode` 掉出场景的远近。

在尝试了触摸响应器之后，看看 `touchesBegan()` 方法中的单行代码。这行代码在每次点击屏幕时向 `playerNode` 的物理体施加一个冲量。冲量的方向和强度取决于你传递给 `applyImpulse()` 方法的向量。这里，你创建了一个 x 值为 0.0（因为你只想沿着 y 轴线性施加冲量）且 y 值为 40.0 的向量，这会产生一个将玩家向重力相反方向弹起的脉冲。

在继续之前，尝试调整创建此向量的 y 值。这能帮助你理解该值如何影响脉冲的大小。完成后，将 y 值恢复为 40.0，让我们继续。

一旦玩家在场景中可见，点击屏幕直到玩家到达可视场景顶部，然后观察它下落。关于 `playerNode` 下落方式，需要注意一点：玩家下落时，仿佛没有任何表面积来减缓其速度。这看起来不太对劲。为了解决这个问题，`SpriteKit` 的 `SKPhysicsBody` 提供了一个 `linearDamping` 属性。此属性默认值为 0.1，用于减小物理体的线性速度，以模拟流体或空气摩擦力。这里，你在模拟空气摩擦力。

要了解如何使用此属性，请在 `playerNode` 定位代码之后立即添加以下代码行，然后再次运行应用程序：
`playerNode.physicsBody?.linearDamping = 1.0`

现在点击屏幕直到玩家到达屏幕顶部，让它再次下落。这一次，`playerNode` 下落的更慢，模拟了在空气中下落时的阻力。

在进入本章的碰撞检测部分之前，请确保你的 `GameScene.swift` 文件看起来如 3-1 所示。

```
import SpriteKit
class GameScene: SKScene {
    let backgroundNode = SKSpriteNode(imageNamed: "Background")
    let playerNode = SKSpriteNode(imageNamed: "Player")
    required init?(coder aDecoder: NSCoder) {
        super.init(coder: aDecoder)
    }
    override init(size: CGSize) {
        super.init(size: size)
        physicsWorld.gravity = CGVector(dx: 0.0, dy: -2.0);
        backgroundColor = SKColor(red: 0.0, green: 0.0, blue: 0.0, alpha: 1.0)
        isUserInteractionEnabled = true
        // adding the background
        backgroundNode.size.width = frame.size.width
        backgroundNode.anchorPoint = CGPoint(x: 0.5, y: 0.0)
        backgroundNode.position = CGPoint(x: size.width / 2.0, y: 0.0)
        addChild(backgroundNode)        
        // add the player
        playerNode.physicsBody = SKPhysicsBody(circleOfRadius: playerNode.size.width / 2)
        playerNode.physicsBody?.isDynamic = true
        playerNode.position = CGPoint(x: size.width / 2.0, y: 80.0)
        playerNode.physicsBody?.linearDamping = 1.0
        addChild(playerNode!)
    }
    override func touchesBegan(_ touches: Set<UITouch>, with event: UIEvent?) {
        playerNode.physicsBody?.applyImpulse(CGVector(dx: 0.0, dy: 40.0))
    }
}
```

**列表 3-1** `GameScene.swift`：`SuperSpaceMan` 主要修改后的 `GameScene`

### 向 `SKNode` 添加碰撞检测

目前，游戏拥有一个响应重力和冲量的 `playerNode`；现在是时候添加另一个玩家可以交互的节点了。在本节中，你将在游戏中添加另一个代表宝珠的精灵。你将首先添加宝珠并将其定位在玩家上方。之后，你将修改宝珠的属性，使玩家和宝珠在游戏中自然交互。最后，你将添加检测玩家与宝珠碰撞的代码，并在它们碰撞时将宝珠从场景中移除。



#### 添加碰撞节点

我们开始吧。首先，你需要在 `GameScene` 中添加一个能量球精灵，位置设定在玩家节点左上方稍远处。在 `sprites.atlas` 图册中，用于能量球的图片名为 `PowerUp`。添加这个精灵没有什么特别之处——操作方式与添加玩家节点完全相同。

首先，在 `playerNode` 声明之后立即添加新节点的声明：

```
let orbNode = SKSpriteNode(imageNamed: "PowerUp")
```

添加 `orbNode` 的声明后，在 `GameScene.init(size: CGSize)` 方法的末尾添加以下代码：

```
orbNode.position = CGPoint(x: 150.0, y: size.height - 25)
orbNode.physicsBody = SKPhysicsBody(circleOfRadius: orbNode.size.width / 2)
orbNode.physicsBody?.isDynamic = false
addChild(orbNode)
```

查看这段代码片段，你会发现它与添加 `playerNode` 的代码相似，但有两点不同。首先，`orbNode` 被放置在场景顶部下方 25 个点、玩家左方稍远处。其次，`orbNode.physicsBody.isDynamic` 属性被设置为 `false`。这是因为你不希望这个节点对其他节点做出反应。你希望 `playerNode` 穿过场景收集能量球作为上升燃料。这就是为什么你要为 `orbNode` 的物理体赋予一个静态体积。

现在继续运行应用，但这次点击屏幕直到 `playerNode` 与 `orbNode` 碰撞。由于 `playerNode` 是动态体，它会弹开并旋转着飞向远处；而 `orbNode` 是静态体，它会保持原地不动。仅用这么少的代码就能实现这样的效果，相当不错。但在继续检测碰撞并移除能量球之前，你还需要做一件事。

你可能会注意到，当 `playerNode` 与 `orbNode` 碰撞时，`playerNode` 开始旋转。在很多情况下这是正确的反应，但对于这个游戏，你希望 `playerNode` 直接穿过能量球而不旋转。SpriteKit 提供了一种简单的方法来防止这种旋转：`physicsBody.allowsRotation` 属性。

在将 `playerNode` 添加到场景之前的代码处，添加以下一行代码：

```
playerNode.physicsBody?.allowsRotation = false
```

现在再次运行应用。这次当玩家与能量球碰撞时，会弹开但不会旋转。

#### 添加碰撞检测

现在你的 `playerNode` 和 `orbNode` 已经能够正确碰撞并做出反应。是时候添加显式代码来检测玩家与能量球之间的碰撞，并在碰撞时从场景中移除能量球。运行游戏时，你确实能看到能量球与玩家之间的碰撞，但现在你要做的是检测碰撞何时发生，并移除参与碰撞的能量球。在后续游戏开发中，你还会添加燃料元素，让能量球为玩家提供燃料，使其越升越高。

为了能够检测 `SKNode` 之间的接触，你首先需要在 `GameScene` 类中实现 SpriteKit 的协议 `SKPhysicsContactDelegate`，然后实现游戏中所需的方法。

`SKPhysicsContactDelegate` 协议定义了两个用于检测 `SKNode` 相互接触的方法：`didBeginContact` 和 `didEndContact()` 方法。以下代码片段展示了该协议：

```
public protocol SKPhysicsContactDelegate : NSObjectProtocol {
    optional public func didBegin(_ contact: SKPhysicsContact)
    optional public func didEnd(_ contact: SKPhysicsContact)
}
```

方法名直接反映了其功能。`didBegin(_ contact: SKPhysicsContact)` 方法在接触开始时被调用，而 `didEnd(_ contact: SKPhysicsContact)` 方法在接触结束时被调用。对于这个游戏，你只关心第一个方法 `didBegin(_ contact: SKPhysicsContact)`，因为你不想让 `playerNode` 穿过能量球。你希望在 `playerNode` 接触能量球的瞬间将其从场景中移除。

回到 `GameScene.swift` 文件，在 `GameScene` 类的结束大括号 `}` 之后创建一个类扩展，添加以下代码片段：

```
extension GameScene: SKPhysicsContactDelegate {
}
```

添加扩展后，在 `GameScene` 类的末尾添加 `didBegin(_ contact: SKPhysicsContact)` 方法的实现：

```
func didBegin(_ contact: SKPhysicsContact) {
    print("发生了接触。")
}
```

最后，你需要让 `GameScene` 成为场景 `physicsWorld.contactDelegate` 的代理。为此，在 `GameScene.init(size: CGSize)` 方法中，紧接在 `super.init(size: size)` 调用之后添加以下一行代码：

```
physicsWorld.contactDelegate = self
```

#### 为 SKPhysicsBody 添加位掩码

所有设置都已就绪，可以接收接触通知了。接下来你需要做的是告诉 SpriteKit 你想要接收哪些对象的接触通知。你可以使用一种称为位掩码的概念来实现。`SKPhysicsBody` 有三个位掩码属性，可用于定义物理体在游戏世界中与其他物理体的交互方式：`collisionBitMask`、`categoryBitMask` 和 `contactTestBitMask`。每个属性在表 3-1 中有详细说明。

**表 3-1. SKPhysicsBody 的三个位掩码属性**

| 属性 | 用途 |
| --- | --- |
| `collisionBitMask` | 定义你的 `SKPhysicsBody` 会碰撞到的碰撞类别。所有其他物理体将被穿透。 |
| `categoryBitMask` | 定义物理体所属的碰撞类别。 |
| `contactTestBitMask` | 确定此物理体与哪些类别发生接触。在你的游戏中，一个例子是与能量球接触。你希望 SpriteKit 在 `playerNode` 与 `orbNode` 接触时通知你。 |


目前应用程序只有两个节点：一个玩家节点和一个能量球节点。这使得对它们进行分类变得容易。你可以将玩家放入`CollisionCategoryPlayer`类别，将所有能量球节点（目前只有一个）放入名为`CollisionCategoryPowerUpOrbs`的类别。这两个类别位掩码定义如下：
```
let CollisionCategoryPlayer  : UInt32 = 0x1 << 1
let CollisionCategoryPowerUpOrbs : UInt32 = 0x1 << 2
```

这里有`CollisionCategoryPlayer`和`CollisionCategoryPowerUpOrbs`两个位掩码，每个都是无符号 32 位整数。这一点很重要，因为碰撞位掩码是 32 位的，你最多只能有 32 个唯一类别。继续在`GameScene`中，紧跟在`orbNode`声明之后、第一个`init()`方法之前添加以下两行：
```
let CollisionCategoryPlayer  : UInt32 = 0x1 << 1
let CollisionCategoryPowerUpOrbs : UInt32 = 0x1 << 2
```

让我们看看如何使用这两个节点设置碰撞检测属性。先从`playerNode`开始。以下三行设置了玩家的碰撞属性：
```
playerNode.physicsBody?.categoryBitMask = CollisionCategoryPlayer
playerNode.physicsBody?.contactTestBitMask = CollisionCategoryPowerUpOrbs
playerNode.physicsBody?.collisionBitMask = 0
```

第一行将`playerNode.physicsBody`的类别位掩码关联到`CollisionCategoryPlayer`。第二行告诉 SpriteKit，每当你的物理体与属于`CollisionCategoryPowerUpOrbs`类别的另一个物理体发生接触时，你想要收到通知。最后一行将`playerNode.physicsBody`的`collisionBitMask`设置为`0`，告诉 SpriteKit 不要为你处理碰撞。游戏将在`didBegin()`方法中自行处理碰撞。继续在`GameScene.init(size: CGSize)`方法中，紧跟在以下行之后添加这三行：
`playerNode.physicsBody?.allowsRotation = false`

接下来，我们来配置`orbNode`的物理体。配置`orbNode`的物理体比配置`playerNode`更简单，只需要两行代码：
```
orbNode.physicsBody?.categoryBitMask = CollisionCategoryPowerUpOrbs
orbNode.physicsBody?.collisionBitMask = 0
```

第一行将`orbNode`的物理体关联到`CollisionCategoryPowerUpOrbs`类别。第二行与配置玩家时一样，设置为`0`，因为你要自己处理碰撞。这里需要注意一点：配置能量球节点时，你没有设置`contactTestBitMask`，因为不需要这样做。由于已经在`playerNode`中设置了接触测试，你仍然会收到接触通知。在`GameScene.init(size: CGSize)`方法中，紧挨在将`orbNode`添加到场景之前，添加这两行：
```
orbNode.physicsBody?.categoryBitMask = CollisionCategoryPowerUpOrbs
orbNode.physicsBody?.collisionBitMask = 0
```

此时，两个节点都已配置好碰撞检测，每当`playerNode`与`orbNode`发生接触时，`didBegin(_contact: SKPhysicsContact)`方法将被调用。让我们测试一下。保存所有更改并运行应用程序，在点击屏幕时注意控制台输出。现在，当玩家与能量球接触时，你会在控制台中看到以下文本：
`There has been contact.`
另外，请注意这次玩家穿过了能量球。这是因为你在两个节点中都将物理体的`collisionBitMask`属性设置为`0`。

#### 收到接触消息时移除能量球

最后要做的，是添加在`playerNode`与能量球接触时移除`orbNode`的功能。为此，你需要做一些修改。首先，如第 2 章所述，`SKNode`有一个`name`属性，用于标识单个节点或一组节点，因此你需要使用该属性来判断玩家是否遇到了能量球。要命名能量球节点，请在`GameScene.init(size: CGSize)`方法中，紧挨在将`orbNode`添加到场景之前，添加以下代码行：
`orbNode.name = "POWER_UP_ORB"`

最后需要做的是，修改`didBegin(_contact: SKPhysicsContact)`方法，检查与玩家`SKNode`接触的节点是否具有等于`"POWER_UP_ORB"`的`name`属性，如果有，则将该节点从场景中移除。修改后的`didBegin(_contact: SKPhysicsContact)`方法如下所示：
```
func didBegin(_ contact: SKPhysicsContact) {
    let nodeB = contact.bodyB.node
    if nodeB?.name == "POWER_UP_ORB" {
        nodeB?.removeFromParent()
    }
}
```

在这个方法中，首先要注意的是传递给方法的参数。这里有一个类型为`SKPhysicsContact`的`contact`参数。`SKPhysicsContact`包含了处理节点接触所需的所有信息。`SKPhysicsContact`类包含五个属性，帮助你确定接触的特性，定义在表 3-2 中。

**表 3-2. SKPhysicsContact 属性**

| 方法 | 用途 |
| --- | --- |
| `bodyA` | `bodyA`属性（`SKPhysicsBody`类型）表示接触中的第一个物体。（这将是`playerNode`。） |
| `bodyB` | `bodyB`属性（`SKPhysicsBody`类型）表示接触中的第二个物体。（这将是`orbNode`。） |
| `contactPoint` | `contactPoint`（`CGPoint`类型）表示场景坐标中两个物理体之间的接触点。 |
| `collisionImpulse` | `contactImpulse`（`CGFloat`类型）以牛顿·秒为单位，指定这两个物体碰撞的力度。 |
| `collisionNormal` | `collisionNormal`（`CGVector`类型）指定碰撞的方向。 |


您感兴趣的是这两个属性中的第二个：`bodyB`。回到`didBegin(_contact: SKPhysicsContact)`方法，注意该方法的第二行。在这里，您引用了碰撞体的`bodyB`属性，正如我们之前提到的，这是一个`SKPhysicsContact`。一旦获得此引用，您就可以通过`bodyB.node`属性获取`SKNode`实例，它代表了碰撞中的第二个节点。获取到碰撞中的第二个`SKNode`后，您可以检查它的名称是否为`POWER_UP_ORB`。如果是，则调用该节点的`removeFromParent()`方法，顾名思义，该方法会将`orbNode`从场景中移除。继续操作，将当前`didBegin(_contact: SKPhysicsContact`方法的内容替换为此版本，并最后一次运行应用程序。这一次，当`playerNode`与`orbNode`碰撞时，`orbNode`将从场景中消失。在本章末尾，新的`GameScene.swift`应如代码清单 3-2 所示。

```swift
import SpriteKit

class GameScene: SKScene {
    let backgroundNode = SKSpriteNode(imageNamed: "Background")
    let playerNode = SKSpriteNode(imageNamed: "Player")
    let orbNode = SKSpriteNode(imageNamed: "PowerUp")
    let CollisionCategoryPlayer: UInt32 = 0x1 << 1
    let CollisionCategoryPowerUpOrbs: UInt32 = 0x1 << 2

    required init?(coder aDecoder: NSCoder) {
        super.init(coder: aDecoder)
    }

    override init(size: CGSize) {
        super.init(size: size)
        physicsWorld.contactDelegate = self
        physicsWorld.gravity = CGVector(dx: 0.0, dy: -2.0)
        backgroundColor = SKColor(red: 0.0, green: 0.0, blue: 0.0, alpha: 1.0)
        isUserInteractionEnabled = true

        // adding the background
        backgroundNode.size.width = frame.size.width
        backgroundNode.anchorPoint = CGPoint(x: 0.5, y: 0.0)
        backgroundNode.position = CGPoint(x: size.width / 2.0, y: 0.0)
        addChild(backgroundNode)

        // add the player
        playerNode.physicsBody = SKPhysicsBody(circleOfRadius: playerNode.size.width / 2)
        playerNode.physicsBody?.isDynamic = true
        playerNode.position = CGPoint(x: size.width / 2.0, y: 80.0)
        playerNode.physicsBody?.linearDamping = 1.0
        playerNode.physicsBody?.allowsRotation = false
        playerNode.physicsBody?.categoryBitMask = CollisionCategoryPlayer
        playerNode.physicsBody?.contactTestBitMask = CollisionCategoryPowerUpOrbs
        playerNode.physicsBody?.collisionBitMask = 0
        addChild(playerNode)

        orbNode.position = CGPoint(x: 150.0, y: size.height - 25)
        orbNode.physicsBody = SKPhysicsBody(circleOfRadius: orbNode.size.width / 2)
        orbNode.physicsBody?.isDynamic = false
        orbNode.physicsBody?.categoryBitMask = CollisionCategoryPowerUpOrbs
        orbNode.physicsBody?.collisionBitMask = 0
        orbNode.name = "POWER_UP_ORB"
        addChild(orbNode)
    }

    override func touchesBegan(_ touches: Set<UITouch>, with event: UIEvent?) {
        playerNode.physicsBody?.applyImpulse(CGVector(dx: 0.0, dy: 40.0))
    }
}

extension GameScene: SKPhysicsContactDelegate {
    func didBegin(_ contact: SKPhysicsContact) {
        let nodeB = contact.bodyB.node
        if nodeB?.name == "POWER_UP_ORB" {
            nodeB?.removeFromParent()
        }
    }
}
```

**代码清单 3-2.** `GameScene.swift`：带有碰撞检测的超级太空人游戏场景

### 总结

本章讨论了 SpriteKit 的物理引擎和碰撞检测。我们先讨论了`SKPhysicsBody`。接着，你开启了游戏世界的重力，这将玩家角色下拉到场景底部。之后，你添加了一个触摸响应器，将`playerNode`向上推回太空。最后，我们讨论了如何处理碰撞。在第 4 章中，你将向场景添加滚动功能，这样玩家就可以在场景中飞得更高，而不会飞出场景顶部。你还将集成加速度计，为玩家提供额外的控制。

© James Goodwill 和 Wesley Matlock 2017  
James Goodwill 和 Wesley Matlock  
《Beginning Swift Games Development for iOS》  
10.1007/978-1-4842-2310-9_4

