# 14. 碰撞检测

James Goodwill¹ and Wesley Matlock²

(1) 美国科罗拉多州海兰兹牧场  
(2) 美国密苏里州堪萨斯城

现在你已经让角色在屏幕上移动，接下来需要一种方法来确定英雄何时找到可收集物品。为此，你将使用碰撞检测来知道不同节点何时相互接触。

### 碰撞检测

现在英雄可以四处移动，并且场景中有多个可收集物品，你需要确定英雄何时与其中一个碰撞。`SceneKit`使用`SCNPhysicsBody`对象为节点添加物理模拟。在渲染场景期间，`SceneKit`会准备执行物理计算的帧。这些计算包括重力、摩擦以及与其他节点的碰撞。

`SCNPhysicsBody`类有几个属性，你需要为每个`SCNNode`设置这些属性才能检测碰撞。首先，你将物理体设置到英雄节点上。由于节点是通过`createMainScene`方法中的`let mainScene = SCNScene(named: "art.scnassets/hero.dae")`调用加载到场景中的，因此你将从这里开始。在`return`语句之前，向该方法添加以下代码行：

```
let heroNode = mainScene.rootNode.childNode(withName: "hero", recursively: true)
heroNode?.physicsBody = SCNPhysicsBody(type: .dynamic, shape: nil)
```

首先，你从主场景中获取了英雄节点。然后，将物理体设置到节点上。`SceneKit`有三种不同类型的物理体：

*   **静态（Static）**：这种类型的物理体不受力或碰撞的影响。你将用它来设置地板、障碍物和墙壁，因为它们会与其他物体碰撞，但自身不会移动。
*   **动态（Dynamic）**：这种类型的物理体受力或碰撞的影响。你将用它来设置太空人和敌人。
*   **运动（Kinematic）**：这种类型不受力或碰撞的影响，但可以在物理世界中引发碰撞。你不会用到这种类型，但它可以用于一个不可见的节点，该节点代表用户触摸物体时的手指。



`Shape`是初始化器中的下一个参数。`Shape`定义了用于碰撞检测的主体。在你的调用中，你需要将`shape`设置为`nil`，这将允许`SceneKit`根据节点的几何属性自动创建物理形状。如果你希望更精确地控制`SceneKit`用于碰撞检测的实际形状，可以将其设置为不同的形状。就你的目的而言，让`SceneKit`定义形状就足够了。清单 14-1 给出了你需要在所有节点上设置的`physicsBody`。请花些时间在每个位置设置每个节点。

然而，如果你现在运行游戏，会发现主角直接坠落并消失在地板下方。你可能已经猜到，由于主角现在有了物理主体，它正在与地板交互，而地板目前还没有物理主体，因此重力直接将主角拉入了虚无区域。让我们来修复这个问题。在`createFloorNode()`方法中，在`return`语句之前将代码更新为以下语句：`floorNode.physicsBody = SCNPhysicsBody(type: .static, shape: nil)`。现在当你运行游戏时，主角将停留在地板上而不会掉下去。

接下来你需要做的是，将物理主体添加到`Collectible`类中的所有可收集物上。这样主角就不会直接穿过它们，而是会撞到它们。为确保你走在正确的道路上，清单 14-1 展示了添加完所有`SCNPhysicsBody`后`Collectible`类的完整代码。

```
//
//  Collectible.swift
//  Swifystein3D
//
//  Copyright © 2016 Apress. All rights reserved.
//

import Foundation
import SceneKit

class Collectible {
    class func pyramidNode() -> SCNNode {
        // 1 创建 SCNGeometry 类型
        let pyramid = SCNPyramid(width: 3.0, height: 6.0, length: 3.0)
        // 2 使用几何体类型创建节点
        let pyramidNode = SCNNode(geometry: pyramid)
        pyramidNode.name = "pyramid"
        // 3 设置节点位置
        let position = SCNVector3(0, 0, 200)
        pyramidNode.position = position
        // 4 为节点赋予颜色
        pyramidNode.geometry?.firstMaterial?.diffuse.contents = UIColor.blue
        pyramidNode.geometry?.firstMaterial?.shininess = 1.0
        pyramidNode.physicsBody = SCNPhysicsBody(type: .static, shape: nil)
        return pyramidNode
    }
    class func sphereNode() -> SCNNode {
        // 1 创建 SCNGeometry 类型
        let sphere = SCNSphere(radius: 6.0)
        // 2 使用几何体类型创建节点
        let sphereNode = SCNNode(geometry: sphere)
        sphereNode.name = "sphere"
        // 3 设置节点位置
        let position  = SCNVector3(0, 6, -200)
        sphereNode.position = position
        // 4 为节点赋予颜色
        sphereNode.geometry?.firstMaterial?.diffuse.contents = #imageLiteral(resourceName: "earthDiffuse")
        sphereNode.geometry?.firstMaterial?.ambient.contents = #imageLiteral(resourceName: "earthAmbient")
        sphereNode.geometry?.firstMaterial?.specular.contents = #imageLiteral(resourceName: "earthSpecular")
        sphereNode.geometry?.firstMaterial?.normal.contents = #imageLiteral(resourceName: "earthNormal")
        sphereNode.geometry?.firstMaterial?.diffuse.mipFilter = SCNFilterMode.linear
        sphereNode.geometry?.firstMaterial?.shininess = 1.0
        sphereNode.physicsBody = SCNPhysicsBody(type: .static, shape: nil)
        return sphereNode
    }
    class func boxNode() -> SCNNode {
        // 1 创建 SCNGeometry 类型
        let box = SCNBox(width: 6, height: 6, length: 6, chamferRadius: 0)
        // 2 使用几何体类型创建节点
        let boxNode = SCNNode(geometry: box)
        boxNode.name = "box"
        // 3 设置节点位置
        let position  = SCNVector3(200, 3.0, 0)
        boxNode.position = position
        // 4 为节点赋予颜色
        var materials = [SCNMaterial]()
        let boxImage = "boxSide"
        for index in 1...6 {
            let material = SCNMaterial()
            material.diffuse.contents = UIImage(named: boxImage + String(index))
            materials.append(material)
        }
        boxNode.geometry?.materials = materials
        boxNode.physicsBody = SCNPhysicsBody(type: .static, shape: nil)
        return boxNode
    }
    class func tubeNode() -> SCNNode {
        // 1 创建 SCNGeometry 类型
        let tube = SCNTube(innerRadius: 8, outerRadius: 10.0, height: 10.0)
        // 2 使用几何体类型创建节点
        let tubeNode = SCNNode(geometry: tube)
        tubeNode.name = "tube"
        // 3 设置节点位置
        let position  = SCNVector3(-200, 1.5, 0)
        tubeNode.position = position
        // 4 为节点赋予颜色
        tubeNode.geometry?.firstMaterial?.diffuse.contents = UIColor.yellow
        tubeNode.geometry?.firstMaterial?.shininess = 1.0
        tubeNode.physicsBody = SCNPhysicsBody(type: .static, shape: nil)
        return tubeNode
    }
    class func cylinderNode() -> SCNNode {
        // 1 创建 SCNGeometry 类型
        let cylinder = SCNCylinder(radius: 6, height: 16)
        // 2 使用几何体类型创建节点
        let cylinderNode = SCNNode(geometry: cylinder)
        cylinderNode.name = "cylinder"
        // 3 设置节点位置
        let position = SCNVector3(300, 8, 300)
        cylinderNode.position = position
        // 4 为节点赋予颜色
        cylinderNode.geometry?.firstMaterial?.diffuse.contents = UIColor.green
        cylinderNode.geometry?.firstMaterial?.shininess = 0.5
        cylinderNode.physicsBody = SCNPhysicsBody(type: .static, shape: nil)
        return cylinderNode
    }
    class func torusNode() -> SCNNode {
        // 1 创建 SCNGeometry 类型
        let torus = SCNTorus(ringRadius: 14, pipeRadius: 4)
        // 2 使用几何体类型创建节点
        let torusNode = SCNNode(geometry: torus)
        // 3 设置节点位置
        let position =  SCNVector3(-300, 3, -300)
        torusNode.position = position
        // 4 为节点赋予颜色
        torusNode.geometry?.firstMaterial?.diffuse.contents = UIColor.orange
        torusNode.geometry?.firstMaterial?.shininess = 1.0
        torusNode.physicsBody = SCNPhysicsBody(type: .static, shape: nil)
        return torusNode
    }
}
```

**清单 14-1.** 为所有对象设置了 `SCNPhysicBody` 的 `Collectible.swift`

让碰撞检测工作的下一步是创建位掩码，这些位掩码将用于确定一个对象是否以及何时触碰另一个对象。碰撞检测基于使用位掩码创建一个表格，以确定两个对象是否应发生碰撞。为此，你将创建一个名为`SharedConstants.swift`的新文件，并使用该文件创建将用于所有对象的位掩码变量。清单 14-2 包含了需要添加到此新文件中的代码：

```
let CollisionCategoryHero = 2
let CollisionCategoryCollectibleLowValue = 4
let CollisionCategoryCollectibleMidValue = 6
let CollisionCategoryCollectibleHighValue = 8
let CollisionCategoryFloor = 10
```

**清单 14-2.** 位掩码的 `SharedConstants.swift` 内容

现在你已经有了将用于位掩码的变量，需要再次更新所有节点。如前所述，这些变量将用于创建一个表格，以确定一个对象是否应与另一个对象碰撞。`SCNPhysicsBody`有几个参数需要设置，才能使碰撞检测工作。这两个参数中的第一个是类别位掩码：

`heroNode?.physicsBody?.categoryBitMask = CollisionCategoryHero`

第二个要设置的参数是碰撞位掩码：

`heroNode?.physicsBody?.collisionBitMask = CollisionCategoryCollectibleLowValue | CollisionCategoryCollectibleMidValue | CollisionCategoryCollectibleHighValue`

基本上，你正在做的是：这个节点是主角，它将与不同价值的可收集节点交互。清单 14-3 显示了必须在每个节点上设置的所有`categoryBitMask`和`collisionBitMask`值。请确保在`SCNPhysicsBody`被初始化并设置在节点上之后再进行此操作。同样重要的是，在将`SCNNode`的`physicsBody`添加为场景的子节点之前对其进行初始化。

```
// 将以下行添加到 GameViewController 的 createMainScene() 方法中，位于 physicsBody 初始化之后：
heroNode?.physicsBody?.categoryBitMask = CollisionCategoryHero
heroNode?.physicsBody?.collisionBitMask = CollisionCategoryCollectibleLowValue | CollisionCategoryCollectibleMidValue | CollisionCategoryCollectibleHighValue

// 将以下行添加到 Collectible 的 pyramidNode() 方法中，位于 physicsBody 初始化之后：
pyramidNode.physicsBody?.categoryBitMask = CollisionCategoryCollectibleLowValue
pyramidNode.physicsBody?.collisionBitMask = CollisionCategoryHero

// 将以下行添加到 Collectible 的 sphereNode() 方法中，位于 physicsBody 初始化之后：
globeNode.physicsBody?.categoryBitMask = CollisionCategoryCollectibleHighValue
globeNode.physicsBody?.collisionBitMask = CollisionCategoryHero

// 将以下行添加到 Collectible 的 boxNode() 方法中，位于 physicsBody 初始化之后：
boxNode.physicsBody?.categoryBitMask = CollisionCategoryCollectibleMidValue
boxNode.physicsBody?.collisionBitMask = CollisionCategoryHero

// 将以下行添加到 Collectible 的 tubeNode() 方法中，位于 physicsBody 初始化之后：
tubeNode.physicsBody?.categoryBitMask = CollisionCategoryCollectibleLowValue
tubeNode.physicsBody?.collisionBitMask = CollisionCategoryHero

// 将以下行添加到 Collectible 的 cylinderNode() 方法中，位于 physicsBody 初始化之后：
cylinderNode.physicsBody?.categoryBitMask = CollisionCategoryCollectibleHighValue
cylinderNode.physicsBody?.collisionBitMask = CollisionCategoryHero

// 将以下行添加到 Collectible 的 torusNode() 方法中，位于 physicsBody 初始化之后：
torusNode.physicsBody?.categoryBitMask = CollisionCategoryCollectibleMidValue
torusNode.physicsBody?.collisionBitMask = CollisionCategoryHero
```

**清单 14-3.** `SCNPhysicsBody` 的 `categoryBitMask` 和 `collisionBitMask`

现在是时候检查你的工作了。构建并运行游戏，你应该能够移动太空人——只不过这次他可以穿过场景中的节点，可以越过一些物体，并且会被墙壁挡住。虽然这很好，但你还需要一种方式让他对这些碰撞做出反应。因为你正在制作一个捉迷藏游戏，你会想知道何时找到了敌人。要找到敌人，你必须抓住或撞到他。这通过使用`SCNPhysicsContactDelegate`来实现。

首先要做的是告诉你的类你想要接收`SCNPhysicsContactDelegate`：

`class GameViewController: UIViewController, SCNSceneRendererDelegate, SCNPhysicsContactDelegate {`

与此结合，你还需要将委托设置给一个将响应这些碰撞的类。在这种情况下，你将把`mainScene`的`contactDelegate`设置为`self`，以便`GameViewController`能够对碰撞做出反应。在`createMainScene()`中，在`return`语句之前添加这一行：

`mainScene?.physicsWorld.contactDelegate = self`

`SCNPhysicsContactDelegate`有三个你可以使用的协议，如清单 14-4 所示。

```
func physicsWorld(_ world: SCNPhysicsWorld, didBegin contact: SCNPhysicsContact) {
    print("didBeginContact")
}

func physicsWorld(_ world: SCNPhysicsWorld, didEnd contact: SCNPhysicsContact) {
    print("didEndContact")
}

func physicsWorld(_ world: SCNPhysicsWorld, didUpdate contact: SCNPhysicsContact) {
    print("didUpdateContact")
}
```

**清单 14-4.** `SCNPhysicsContact` 协议

你应该将清单 14-4 添加到你的`GameViewController`中。这次当你运行游戏时，当你的太空人移动并与环境交互时，你将收到通知。不过，对于这个游戏，你只会使用`didBeginContact`协议。此时，你将检查接触对象是否包含主角节点和可收集物节点。为此，你将保持简单，并在清单 14-5 中检查`nodeB`以确定发生了哪种类型的接触。

```
func physicsWorld(_ world: SCNPhysicsWorld, didBegin contact: SCNPhysicsContact) {
    switch contact.nodeB.physicsBody!.collisionBitMask {
    case CollisionCategorycollectibleLowValue:
        print("Hit a low value collectible.")
    case CollisionCategorycollectibleMidValue:
        print("Hit a mid value collectible.")
    case CollisionCategorycollectibleHighValue:
        print("Hit a high value collectible.")
    default:
        print("Hit something other than a collectible.")
    }
}
```

**清单 14-5.** `SCNPhysicsWorld` 的 `didBeginContact` 协议

现在你只需在碰到敌人或障碍物时打印一行信息。所以，现在运行你的游戏，让太空人在游戏场地上移动。留意控制台日志，当你碰到障碍物或敌人时，会输出相应的信息。



### 摘要

在本章中，你做了大量工作让太空人动起来。现在，太空人能够与物体发生碰撞，而不是直接穿过它们。至此，捉迷藏游戏已接近完成。在第 15 章中，你将使用这个碰撞协议来更新分数。即将创建的记分板将位于 SpriteKit 视图中。这样一来，你就能了解如何将 SpriteKit 视图与 SceneKit 场景结合使用。© James Goodwill 和 Wesley Matlock 2017，James Goodwill 和 Wesley Matlock，《Beginning Swift Games Development for iOS》10.1007/978-1-4842-2310-9_15

