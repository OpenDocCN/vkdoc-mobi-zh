# 9. SpriteKit 最佳实践

James Goodwill¹ 和 Wesley Matlock²  
(1) 美国科罗拉多州海兰兹牧场  
(2) 美国密苏里州堪萨斯城  

在本章中，你将学习一些 SpriteKit 最佳实践；具体来说，你将看到如何创建自己的 `SKSpriteNode` 子类，以便更好地重用节点。接着你将修改游戏，将所有精灵加载到单个纹理图集中，以便在创建未来所有精灵时引用该图集。之后，你将把部分游戏数据外部化，以便设计人员和测试人员能够调整游戏玩法。最后，你将通过移除所有掉出屏幕底部的节点来修剪节点树，从而结束本章内容。

### 通过子类化创建自己的节点



我们想讨论的第一个最佳实践是将精灵节点重构为它们各自的类。这样做不仅能清理场景代码，还能将每个节点的特定代码封装到它自己的类中。可以抽象到独立类中的三个节点分别是玩家节点、能量球节点和黑洞节点。在开始这个过程之前，你需要创建一个新文件来存放常量。这个文件的目的是保存所有将在各个节点间使用的碰撞类别。创建这个新文件并将其命名为`SharedConstants.swift`。文件就位后，将所有碰撞类别从`GameScene.swift`文件移动到该文件中。完成后，你的新文件应如代码清单 9-1 所示。

```swift
let CollisionCategoryPlayer : UInt32 = 0x1 << 1
let CollisionCategoryPowerUpOrbs : UInt32 = 0x1 << 2
let CollisionCategoryBlackHoles : UInt32 = 0x1 << 3
```
*代码清单 9-1. `SharedConstants.swift`：用于存储多个类中使用的常量的文件*

设置好常量后，再创建另一个名为`SpaceMan.swift`的新文件。这个文件将存放你重构后的`playerNode`的类。创建文件后，将代码清单 9-2 中的代码复制进去并保存。

```swift
import Foundation
import SpriteKit

class SpaceMan: SKSpriteNode {
    required init?(coder aDecoder: NSCoder) {
        fatalError("init(coder:) has not been implemented")
    }

    init() {
        let texture = SKTexture(imageNamed: "Player")
        super.init(texture: texture, color: UIColor.clear, size: texture.size())
        physicsBody = SKPhysicsBody(circleOfRadius: size.width / 2)
        physicsBody?.isDynamic = false
        physicsBody?.linearDamping = 1.0
        physicsBody?.allowsRotation = false
        physicsBody?.categoryBitMask = CollisionCategoryPlayer
        physicsBody?.contactTestBitMask = CollisionCategoryPowerUpOrbs | CollisionCategoryBlackHoles
        physicsBody?.collisionBitMask = 0
    }
}
```
*代码清单 9-2. `SpaceMan.swift`：新的`SpaceMan`类*

`SpaceMan`类继承自`SKSpriteNode`，所有与设置太空人纹理和`physicsBody`相关的代码都已移入太空人的`init()`方法中。需要注意的是，在`init()`方法的开头，我们使用`"Player"`图像创建了一个纹理，然后调用了所需的`super.init()`方法。这是因为我们正在子类化`SKSpriteNode`，而这是必需的初始化方法。我们将在本章后面的部分更详细地讨论纹理。现在我们已经将所有内容移到了这个类中，在游戏场景中使用它会容易得多。

一旦你有了新的`SpaceMan`类，就可以移除`GameScene`中以下九行代码：

```swift
playerNode.physicsBody = SKPhysicsBody(circleOfRadius: playerNode.size.width / 2)
playerNode.physicsBody?.dynamic = false
playerNode.position = CGPoint(x: size.width / 2.0, y: 220.0)
playerNode.physicsBody?.linearDamping = 1.0
playerNode.physicsBody?.allowsRotation = false
playerNode.physicsBody?.categoryBitMask = CollisionCategoryPlayer
playerNode.physicsBody?.contactTestBitMask = CollisionCategoryPowerUpOrbs | CollisionCategoryBlackHoles
playerNode.physicsBody?.collisionBitMask = 0
```

从`GameScene`的`init(size: CGSize)`方法中移除上述代码片段后，请确保保留下面这行代码：

```swift
playerNode.position = CGPoint(x: size.width / 2.0, y: 220.0)
```

使用新的`SpaceMan`类，你需要做的最后一步是将节点的声明从：

```swift
let playerNode = SKSpriteNode(imageNamed: "Player")
```

改为：

```swift
let playerNode = SpaceMan()
```

接下来你需要创建的类，是一个封装所有与能量球节点相关代码的类。为此，创建另一个名为`Orb.swift`的文件，并将代码清单 9-3 的内容复制进去。

```swift
import Foundation
import SpriteKit

class Orb: SKSpriteNode {
    required init?(coder aDecoder: NSCoder) {
        fatalError("init(coder:) has not been implemented")
    }

    init() {
        let texture = SKTexture(imageNamed: "PowerUp")
        super.init(texture: texture,
                   color: UIColor.clear,
                   size: texture.size())
        physicsBody =
            SKPhysicsBody(circleOfRadius: self.size.width / 2)
        physicsBody?.isDynamic = false
        physicsBody?.categoryBitMask = CollisionCategoryPowerUpOrbs
        physicsBody?.collisionBitMask = 0
        name = "POWER_UP_ORB"
    }
}
```
*代码清单 9-3. `Orb.swift`：新的`Orb`类*

这个新的`Orb`类，与`SpaceMan`类非常相似，包含了所有设置节点纹理和其`physicsBody`的代码。一旦新的`Orb`类就位，你就可以将`addOrbsToForeground()`方法修改为如下简化版本：

```swift
func addOrbsToForeground() {
    var orbNodePosition =
          CGPoint(x: playerNode.position.x, y: playerNode.position.y + 100)
    var orbXShift : CGFloat = -1.0
    for _ in 0...49 {
        // new code to use an orb
        let orbNode = Orb()
        if orbNodePosition.x - (orbNode.size.width * 2) <= 0 {
            orbXShift = 1.0
        }
        if orbNodePosition.x + orbNode.size.width >= size.width {
            orbXShift = -1.0
        }
        orbNodePosition.x += 40.0 * orbXShift
        orbNodePosition.y += 120
        orbNode.position = orbNodePosition
        foregroundNode.addChild(orbNode)
    }
}
```

`addOrbsToForeground()`方法现在简单多了。它只执行三个步骤：创建每个能量球节点，设置它们各自的位置，然后将它们添加到场景中。

你要创建的最后一个节点类是`BlackHole`类。正如你可能猜到的，这个类将包含所有与黑洞相关的代码。创建另一个名为`BlackHole.swift`的新文件，并将代码清单 9-4 的内容复制进去。

```swift
import Foundation
import SpriteKit

class BlackHole: SKSpriteNode {
    required init?(coder aDecoder: NSCoder) {
        fatalError("init(coder:) has not been implemented")
    }

    init() {
        let frame0 = SKTexture(imageNamed: "BlackHole0")
        let frame1 = SKTexture(imageNamed: "BlackHole1")
        let frame2 = SKTexture(imageNamed: "BlackHole2")
        let frame3 = SKTexture(imageNamed: "BlackHole3")
        let frame4 = SKTexture(imageNamed: "BlackHole4")
        let blackHoleTextures = [frame0, frame1, frame2, frame3, frame4]
        let animateAction =
            SKAction.animate(with: blackHoleTextures, timePerFrame: 0.2)
        let rotateAction = SKAction.repeatForever(animateAction)
        super.init(texture: frame0,
                   color: UIColor.clear,
                   size: frame0.size())
        physicsBody = SKPhysicsBody(circleOfRadius: size.width / 2)
        physicsBody?.isDynamic = false
        physicsBody?.categoryBitMask = CollisionCategoryBlackHoles
        physicsBody?.collisionBitMask = 0
        name = "BLACK_HOLE"
        run(rotateAction)
    }
}
```
*代码清单 9-4. `BlackHole.swift`：新的`BlackHole`类*

一旦新的`BlackHole`类就位，你就可以将`addBlackHolesToForeground()`方法修改为如下简化版本：

```swift
func addBlackHolesToForeground() {
    let moveLeftAction = SKAction.moveTo(x: 0.0, duration: 2.0)
    let moveRightAction = SKAction.moveTo(x: size.width, duration: 2.0)
    let actionSequence = SKAction.sequence([moveLeftAction, moveRightAction])
    let moveAction = SKAction.repeatForever(actionSequence)
    for i in 1...10 {
        // new black hole usage code
        let blackHoleNode = BlackHole()
        blackHoleNode.position = CGPoint(x: size.width - 80.0, y: 600.0 * CGFloat(i))
        blackHoleNode.run(moveAction)
        foregroundNode.addChild(blackHoleNode)
    }
}
```

将这些节点移动到它们自己的类中，使得每个节点的重用更加容易，同时也清理了`GameScene`。

### 重用纹理



### 使用单一的 `SKTextureAtlas` 实例

另一个最佳实践是使用单一 `SKTextureAtlas` 实例来加载所有精灵图像，并在设置所有精灵的纹理时重复使用该图集。第一步是创建一个 `SKTextureAtlas` 并将其传递给节点，以便它们可以检索自己的纹理。

在 `GameScene` 类的第一个 `init()` 方法之前立即添加以下代码行：

```swift
let textureAtlas = SKTextureAtlas(named: "sprites.atlas")
```

创建纹理图集后，你需要修改每个 `SKSpriteNode` 的 `init()` 方法，使其接受一个 `SKTextureAtlas` 作为参数。一旦 `SKSpriteNode` 获得了对 `SKTextureAtlas` 的引用，你就可以更改每个节点中的纹理加载代码，以使用这个传入的 `textureAtlas`。

以下 `init()` 方法展示了在 `SpaceMan` 类中进行的这一修改：

```swift
init(textureAtlas: SKTextureAtlas) {
    let texture = textureAtlas.textureNamed("Player")
    super.init(texture: texture, color: UIColor.clear, size: texture.size())
    physicsBody = SKPhysicsBody(circleOfRadius: size.width / 2)
    physicsBody?.isDynamic = false
    physicsBody?.linearDamping = 1.0
    physicsBody?.allowsRotation = false
    physicsBody?.categoryBitMask = CollisionCategoryPlayer
    physicsBody?.contactTestBitMask =
        CollisionCategoryPowerUpOrbs | CollisionCategoryBlackHoles
    physicsBody?.collisionBitMask = 0
}
```

请注意，`init()` 方法的参数列表现在接受一个 `SKTextureAtlas` 参数，并且 `init()` 方法的第一行使用这个 `SKTextureAtlas` 来加载 `Player` 纹理。另外注意，`init()` 方法不再重写默认的 `init()`，因此我们去掉了 `override` 关键字。

对 `SpaceMan.swift` 的 `init()` 方法进行此更改，然后我们回到 `GameScene` 类并更改 `playerNode` 的声明。要使用新的 `SpaceMan.init()` 方法，你需要做一些更改。

在 `GameScene` 类中，首先将类顶部的 `playerNode` 构造代码更改为如下所示：

```swift
var playerNode: SpaceMan!
```

接下来，在 `init()` 方法中添加一行代码，用于用正确的 `SpaceMan` `SKSpriteNode` 实例来初始化 `playerNode`。在设置 `playerNode.position` 的代码上方，添加以下行：

```swift
playerNode = SpaceMan(textureAtlas: textureAtlas)
```

完成所有 `SpaceMan` 的更改后，我们继续对 `Orb` 和 `BlackHole` 类进行相同的修改。首先，更改 `Orb` 的 `init()` 方法，使其接受一个 `SKTextureAtlas`：

```swift
init(textureAtlas: SKTextureAtlas) {
    let texture = textureAtlas.textureNamed("PowerUp")
    super.init(texture: texture, color: UIColor.clear, size: texture.size())
    physicsBody = SKPhysicsBody(circleOfRadius: size.width / 2)
    physicsBody?.isDynamic = false
    physicsBody?.categoryBitMask = CollisionCategoryPowerUpOrbs
    physicsBody?.collisionBitMask = 0
    name = "POWER_UP_ORB"
}
```

更改 `Orb` 的 `init()` 方法后，将 `GameScene` 的 `addOrbsToForeground()` 方法修改为如下所示：

```swift
func addOrbsToForeground() {
    var orbNodePosition =
        CGPoint(x: playerNode.position.x, y: playerNode.position.y + 100)
    var orbXShift : CGFloat = -1.0
    for _ in 0...49 {
        let orbNode = Orb(textureAtlas: SKTextureAtlas(named: "sprites.atlas"))
        if orbNodePosition.x - (orbNode.size.width * 2) <= 0 {
            orbXShift = 1.0
        }
        if orbNodePosition.x + orbNode.size.width >= size.width {
            orbXShift = -1.0
        }
        orbNodePosition.x += 40.0 * orbXShift
        orbNodePosition.y += 120
        orbNode.position = orbNodePosition
        foregroundNode.addChild(orbNode)
    }
}
```

接下来，更改 `BlackHole` 的 `init()` 方法，使其接受一个 `SKTextureAtlas`，然后使用这个纹理图集加载所有后续的纹理。此更改如下代码片段所示：

```swift
init(textureAtlas: SKTextureAtlas) {
    let frame0 = textureAtlas.textureNamed("BlackHole0")
    let frame1 = textureAtlas.textureNamed("BlackHole1")
    let frame2 = textureAtlas.textureNamed("BlackHole2")
    let frame3 = textureAtlas.textureNamed("BlackHole3")
    let frame4 = textureAtlas.textureNamed("BlackHole4")
    let blackHoleTextures = [frame0, frame1, frame2, frame3, frame4];
    let animateAction = SKAction.animate(with: blackHoleTextures, timePerFrame: 0.2)
    let rotateAction = SKAction.repeatForever(animateAction)
    super.init(texture: frame0, color: UIColor.clear, size: frame0.size())
    physicsBody = SKPhysicsBody(circleOfRadius: size.width / 2)
    physicsBody?.isDynamic = false
    physicsBody?.categoryBitMask = CollisionCategoryBlackHoles
    physicsBody?.collisionBitMask = 0
    name = "BLACK_HOLE"
    run(rotateAction)
}
```

最后，修改 `GameScene` 的 `addBlackHolesToForeground()` 方法，使其将 `SKTextureAtlas` 传递给 `BlackHole`，如下所示：

```swift
func addBlackHolesToForeground() {
    let moveLeftAction = SKAction.moveTo(x: 0.0, duration: 2.0)
    let moveRightAction = SKAction.moveTo(x: size.width, duration: 2.0)
    let actionSequence = SKAction.sequence([moveLeftAction, moveRightAction])
    let moveAction = SKAction.repeatForever(actionSequence)
    for i in 1...10 {
        // 新的黑洞使用代码
        let blackHoleNode = BlackHole(textureAtlas:
            SKTextureAtlas(named: "sprites.atlas"))
        blackHoleNode.position = CGPoint(x: size.width - 80.0,
                                         y: 600.0 * CGFloat(i))
        blackHoleNode.run(moveAction)
        foregroundNode.addChild(blackHoleNode)
    }
}
```

至此，所有 `SKSpriteNode` 都重复使用了同一个 `SKTextureAtlas`，这将加快每个节点纹理的检索速度。

### 外部化你的游戏数据



接下来要讨论的最佳实践不一定会提升游戏性能，但有助于开发和测试。到目前为止，每个游戏节点的位置都硬编码在`SuperSpaceMan`的 Swift 代码中。如果你正在开发一个简单的游戏，并且同时担任设计师和开发者，这样没问题；但如果你的团队有明确分工，你可能希望让设计师或测试人员无需修改 Swift 代码就能调整游戏玩法。一种实现方式是将游戏节点的位置外部化。在`SuperSpaceMan`游戏中，一个简单的做法是把能量球和黑洞的位置移到 plist 文件中。然后你可以通过`NSBundle`加载节点位置。这样设计师或测试人员只需修改 plist 文件就能改变整个游戏的布局。

下面是一个包含前三个能量球节点位置的 plist 示例：

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>positions</key>
    <array>
    <dict>
        <key>x</key>
        <real>120.0</real>
        <key>y</key>
        <real>440.0</real>
    </dict>
    <dict>
        <key>x</key>
        <real>80.0</real>
        <key>y</key>
        <real>560.0</real>
    </dict>
    <dict>
        <key>x</key>
        <real>120.0</real>
        <key>y</key>
        <real>680.0</real>
    </dict>
    ...
    </array>
</dict>
</plist>
```

在这个文件中，你可以看到一个字典数组，每个字典包含能量球节点的 x 坐标和 y 坐标。无需自己创建这个文件。你可以在之前下载的、包含精灵图片的 zip 压缩包中找到`orbs.plist`和`blackholes.plist`文件。将这两个文件复制到项目的`SupportingFiles`组中，然后我们继续使用它们。

将两个 plist 文件复制到项目后，你可以通过主 bundle 加载它们。看看下面修改后的`addOrbsToForeground()`方法：

```
func addOrbsToForeground() {
    let orbPlistPath = Bundle.main.path(forResource: "orbs", ofType: "plist")
    let orbDataDictionary = NSDictionary(contentsOfFile: orbPlistPath!)
    if let positionDictionary = orbDataDictionary {
        let positions = positionDictionary.object(forKey: "positions") as! NSArray
        for position in positions {
            let orbNode = Orb(textureAtlas: SKTextureAtlas(named: "sprites.atlas"))
            let x = (position as AnyObject).object(forKey: "x") as! CGFloat
            let y = (position as AnyObject).object(forKey: "y") as! CGFloat
            orbNode.position = CGPoint(x: x, y: y)
            foregroundNode.addChild(orbNode)
        }
    }
}
```

在新的`addOrbsToForeground()`方法中，你可以看到它首先加载`orbs.plist`的内容。然后从字典中获取位置数组，最后遍历所有位置，将每个`orbNode`添加到对应位置的`foregroundNode`上。现在你可以通过修改 plist 来改变所有能量球节点的数量和布局。

对`addOrbsToForeground()`方法进行这些修改，然后对黑洞做同样的处理。你已经将`blackholes.plist`文件复制到项目中，因此可以跳过复制步骤，直接修改`addBlackHolesToForeground()`方法，从 plist 加载黑洞位置。新的`addBlackHolesToForeground()`方法如下：

```
func addBlackHolesToForeground() {
    let moveLeftAction = SKAction.moveTo(x: 0.0, duration: 2.0)
    let moveRightAction = SKAction.moveTo(x: size.width, duration: 2.0)
    let actionSequence = SKAction.sequence([moveLeftAction, moveRightAction])
    let moveAction = SKAction.repeatForever(actionSequence)
    let blackHolePlistPath = Bundle.main.path(forResource: "blackholes", ofType: "plist")
    let blackHoleDataDictionary = NSDictionary(contentsOfFile: blackHolePlistPath!)
    if let positionDictionary = blackHoleDataDictionary {
        let positions = positionDictionary.object(forKey: "positions") as! NSArray
        for position in positions {
            let blackHoleNode = BlackHole(textureAtlas: SKTextureAtlas(named: "sprites.atlas"))
            let x = (position as AnyObject).object(forKey: "x") as! CGFloat
            let y = (position as AnyObject).object(forKey: "y") as! CGFloat
            blackHoleNode.position = CGPoint(x: x, y: y)
            blackHoleNode.run(moveAction)
            foregroundNode.addChild(blackHoleNode)
        }
    }
}
```

请注意，现在的`addBlackHolesToForeground()`方法与`addOrbsToForeground()`方法类似，都是从 plist 中读取所有黑洞节点位置，然后将每个`blackHoleNode`添加到读取到的对应位置。进行修改后再次运行游戏。你会发现没有变化，但现在那些几乎没有开发经验的人也能完全改变游戏了。



### 保持节点树的精简

在 `SuperSpaceMan` 游戏中要实现的最佳实践是，移除所有掉落至可见场景底部的 `SKNode`。从游戏中移除不必要的节点，能够提升节点树渲染的整体性能，并减少节点树中容纳所有节点所占用的内存。要从 `GameScene` 中移除不必要的节点，一个简单的方法是创建一个方法，移除所有具有指定名称且位于 `playerNode` 下方一个场景长度之下的节点。请看以下 `removeOutOfSceneNodesWithName()` 方法：

```
func removeOutOfSceneNodesWithName(_ name: String) {
    foregroundNode.enumerateChildNodes(withName: name, using: {
        node, stop in
        if self.playerNode.position.y - node.position.y > self.size.height {
            node.removeFromParent()
        }
    })
}
```

该方法接受一个代表要测试的每个 `SKNode` 名称的单个 `String`，并使用 `enumerateChildNodesWithName()` 方法来检查这些节点是否位于 `playerNode` 下方一个场景长度的位置。如果返回的节点位置超出这个长度，则将其从场景中移除。关于这个方法有一点需要注意：在 `enumerateChildNodesWithName()` 方法内部，它首先检查 `playerNode` 是否为 `nil`。如果 `playerNode` 为 `nil`，则通过将 `stop.memory` 属性设置为 `true` 来停止对子节点的搜索。将此方法添加到 `GameScene` 的底部。

要使用 `removeOutOfSceneNodesWithName()`，你需要在 `GameScene` 的 `update()` 方法底部，为每个可以从场景中移除的节点添加对该方法的调用。在本例中，可能掉落至场景外的两个节点是 `BLACK_HOLE` 和 `POWER_UP_ORB` 节点。请看修改后的 `update()` 方法，该方法末尾添加了这两个调用，如下所示：

```
override func update(_ currentTime: TimeInterval) {
    if playerNode.position.y >= 180.0 &&
        playerNode.position.y < 6400.0 {
        backgroundNode.position =
            CGPoint(x: backgroundNode.position.x,
                    y: -((playerNode.position.y - 180.0)/8))
        backgroundStarsNode.position =
            CGPoint(x: backgroundStarsNode.position.x,
                    y: -((playerNode.position.y - 180.0)/6))
        backgroundPlanetNode.position =
            CGPoint(x: backgroundPlanetNode.position.x,
                    y: -((playerNode.position.y - 180.0)/8))
        foregroundNode.position =
            CGPoint(x: foregroundNode.position.x,
                    y: -(playerNode.position.y - 180.0))
    }
    else if playerNode.position.y > 7000.0 {
        gameOverWithResult(true)
    }
    else if playerNode.position.y + playerNode.size.height < 0.0 {
        gameOverWithResult(false)
    }
    removeOutOfSceneNodesWithName("BLACK_HOLE")
    removeOutOfSceneNodesWithName("POWER_UP_ORB")
}
```

新的 `update()` 方法现在会在每次场景渲染结束时调用 `removeOutOfSceneNodesWithName()` 方法，移除所有不必要的节点。对 `update()` 方法进行此修改，并最后一次运行 `SuperSpaceMan` 游戏。这次运行游戏时，点击屏幕直到即将赢得游戏，然后停止点击，使 `playerNode` 开始下落。这次在你下落的过程中，之前经过的能量球和黑洞节点已从场景中被移除。

### 总结

在本章中，你学习了一些 SpriteKit 的最佳实践，包括如何创建你自己的 `SKSpriteNode` 子类，以便更好地复用节点。接着，你将游戏改为将所有精灵加载到单一纹理中。之后，你学习了将游戏数据外部化。最后，你修剪了节点树，移除了所有掉落至屏幕底部的节点。在第 10 章中，你将开启 SceneKit 的学习之旅。祝愉快。

