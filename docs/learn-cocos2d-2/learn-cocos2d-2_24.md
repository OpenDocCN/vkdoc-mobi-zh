# `@end`

血条根据其父级 `Enemy` 对象的可见状态来切换自身的可见性。`reset` 方法会将血条精灵放置在 `Enemy` 精灵头顶正上方的适当位置。由于血条是敌人的子节点，只需相对于敌人精灵的 (0,0) 位置（默认是左下角）进行偏移即可。同时，由于生命值减少是通过修改 `scaleX` 属性来显示的，因此也需要将其重置为默认比例。

在 `update` 方法中，只有当父级可见时，`HealthbarComponent` 才会首先断言父级属于 `Enemy` 类。由于该组件依赖于 `Enemy` 类及其子类中才有的特定属性，因此需要确保父类是正确类型。将 `scaleX` 属性修改为当前生命值除以初始生命值的百分比。由于目前无法精确获知生命值变化的时刻，因此每帧都会进行计算，无论是否有必要。这里的性能开销很小，但对于更复杂的计算，最好在 `Enemy` 类的 `onHit` 方法中调用 `HealthbarComponent`。

**注意** `enemy.initialHitPoints` 除数被强制转换为 `float` 类型。否则，除法运算将变成整数除法，由于整数无法表示小数且总是向下取整，结果将永远是 0。将除数转换为 `float` 数据类型可以确保除法以浮点数精度进行，从而得到浮点数值。

在 `Enemy` 类的 `init` 方法中，只有当敌人类型为 `EnemyTypeBoss` 时，`HealthbarComponent` 才会与其他组件一起被添加：

```
    // 创建游戏逻辑组件
    [self addChild:[StandardMoveComponent node]];

    StandardShootComponent* shootComponent = [StandardShootComponent node];
    shootComponent.shootFrequency = shootFrequency;
    shootComponent.bulletFrameName = bulletFrameName;
    [self addChild:shootComponent];

    if (type == EnemyTypeBoss)
    {
        HealthbarComponent* healthbar = [HealthbarComponent spriteWithSpriteFrameName:@"healthbar.png"];
        [self addChild:healthbar];
    }
```

此外，`spawn` 方法已扩展，包括将生命值重置为初始值，并调用添加到敌人上的任何可能的 `HealthbarComponent` 的 `reset` 方法。此处省略了显式检查敌人是否为 Boss 类型，因为 `HealthbarComponent` 是通用的，任何类型的敌人都可以使用。

```
-(void) spawn
{
    // 选择屏幕右侧外部的一个出生位置
    CGSize screenSize = [CCDirector sharedDirector].winSize;
    CGSize spriteSize = self.contentSize;
    float xPos = screenSize.width + spriteSize.width * 0.5f;
    float yPos = CCRANDOM_0_1() * (screenSize.height - spriteSize.height) +
        spriteSize.height * 0.5f;
    self.position = CGPointMake(xPos, yPos);

    // 重置生命值
    hitPoints = initialHitPoints;

    // 最后设置自身为可见，这也会将敌人标记为"使用中"
    self.visible = YES;

    // 重置某些组件
    for (CCNode* node in self.children)
    {
        if ([node isKindOfClass:[HealthbarComponent class]])
        {
            HealthbarComponent* healthbar = (HealthbarComponent*)node;
            [healthbar reset];
        }
    }
}
```

现在你可以运行应用并等待 Boss 出现。每个 Boss 头顶都会有一个红色血条，随着被子弹击中的次数增加，血条宽度会逐渐减少。

## 总结

制作一个完整且精良的游戏需要付出相当的努力——这涉及到大量的重构、修改现有代码以改进设计，并允许更多功能和谐共存。

在本章中，你学习了诸如 `BulletCache` 和 `EnemyCache` 这类类的价值，它们管理着某些类所有实例，使得你可以从一个中心点轻松访问它们。而将对象进行池化管理有助于提升性能。

通过利用组件类和 cocos2d 的节点层次结构，你可以创建具有特定功能的即插即用类。这有助于你使用组合而非继承的方式来构建游戏对象。这是一种编写游戏逻辑代码的更加灵活的方式，并能带来更好的代码复用。

最后，你学习了如何射击敌人，以及 `BulletCache` 和 `EnemyCache` 类如何以直接的方式帮助完成此类任务。而 `HealthbarComponent` 则完美展示了组件系统的工作原理。

目前这个游戏还有一些待你完善的地方。首先，也是最重要的，玩家还没有被击中。你可能还想给巡洋舰怪物添加血条，并为 Boss 怪物的行为编写专门的移动和射击组件。总体而言，这是你自己的横版卷轴射击游戏的一个绝佳起点，只待你去改进它。

在下一章中，我将向你展示如何通过粒子效果为这款射击游戏增添视觉画面。

