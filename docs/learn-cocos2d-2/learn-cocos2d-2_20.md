# `EnemyCache` 类简介

我刚刚提到了 `EnemyCache` 类，这也是 ShootEmUp02 项目中的新类。顾名思义，它应该会让你想起 `BulletCache` 类，后者同样持有大量预初始化的对象，以便快速且轻松地重复使用。这避免了在游戏过程中创建和释放对象，而创建和释放对象可能是造成轻微性能卡顿的根源。特别是对于动作游戏而言，这些小卡顿会对玩家体验产生毁灭性的影响。话虽如此，我们来看一下 代码清单 8-10 中 `EnemyCache` 那个平淡无奇的头文件。

***代码清单 8-10.***  `EnemyCache` 类的 `@interface`

```
#import < Foundation/Foundation.h>
#import "cocos2d.h"

@interface EnemyCache : CCNode
{
    CCSpriteBatchNode* batch;
    NSMutableArray* enemies;

int updateCount;
}
@end
```

在包含了所有敌人精灵的 `spriteBatch` 之后，是一个 `enemies` 的 `NSMutableArray`，它存储了每种类型敌人的列表。`updateCount` 变量每帧递增，用于以固定的时间间隔生成敌人。`EnemyCache` 的 `init` 方法与 `BulletCache` 的 `init` 非常相似，都初始化了 `CCSpriteBatchNode`：

```
-(id) init
{
    if ((self = [super init]))
    {
        // 从我们使用的纹理图集中获取任意图像
        CCSpriteFrame* frame = [[CCSpriteFrameCache sharedSpriteFrameCache]←
        spriteFrameByName:@"monster-a.png"];
        batch = [CCSpriteBatchNode batchNodeWithTexture:frame.texture];
        [self addChild:batch];
        [self initEnemies];
        [self scheduleUpdate];
    }

return self;
}
```

但用于初始化敌人的代码稍显复杂，因此我将其提取到单独的方法中，如 代码清单 8-11 所示。

***代码清单 8-11.***  初始化敌人池以备后续复用

```
-(void) initEnemies
{
    // 创建包含每种敌人类型子数组的 enemies 数组
    enemies = [NSMutableArray arrayWithCapacity:EnemyType_MAX];

// 为每种类型创建子数组
    for (NSUInteger i = 0; i < EnemyType_MAX; i++)
    {
     // 根据敌人类型，设定数组容量以容纳所需数量的敌人
     NSUInteger capacity;
     switch (i)
     {
         case EnemyTypeUFO:
            capacity = 6;
            break;
         case EnemyTypeCruiser:
            capacity = 3;
            break;
         case EnemyTypeBoss:
            capacity = 1;
            break;

default:
            [NSException exceptionWithName:@"EnemyCache Exception"
                                   reason:@"unhandled enemy type"
                                userInfo:nil];
            break;
            }

// 无需 alloc，因为 enemies 数组会 retain 任何添加进来的对象
     NSMutableArray* enemiesOfType = [NSMutableArray arrayWithCapacity:capacity];
     [enemies addObject:enemiesOfType];

for (NSUInteger j = 0; j < capacity; j++)
     {
         Enemy * enemy = [Enemy enemyWithType:i];
         [batch addChild:enemy z:0 tag:i];
         [enemiesOfType addObject:enemy];
     }
    }
}
```

这里有趣的部分在于，`NSMutableArray* enemies` 自身也包含了 `NSMutableArray*` 对象，每个敌人类型对应一个。这被称为二维数组——即数组的数组。

每个 `enemiesOfType`（`NSMutableArray`）的初始容量也决定了同一时间屏幕上最多能出现多少个该类型的敌人。通过这种方式，你可以控制屏幕上敌人的最大数量。然后，每个 `enemiesOfType`（`NSMutableArray`）都像其他任何普通对象一样，通过 `addObject` 被添加到 `enemies`（`NSMutableArray`）中。如果你愿意，你可以用这种方式创建深层层级结构。事实上，cocos2d 的节点层级就是建立在包含 `children` 实例变量的 `CCNode` 类之上的，而这个 `children` 实例变量本身也是一个数组，可以包含更多的 `CCNode` 类，以此类推。我前面提到过，cocos2d 内部使用 `CCArray` 类。它是 `NSMutableArray` 的一个优化替代类，但正如所有优化一样，它也存在一些缺点和兼容性问题。使用 `NSMutableArray` 始终是安全的选择。

根据为 `enemiesOfType` 设定的初始容量，创建所需数量的敌人，将其添加到 `CCSpriteBatchNode` 中，同时也添加到对应的 `enemiesOfType`（`NSMutableArray`）中。虽然也可以通过 `CCSpriteBatchNode` 访问这些敌人，但在单独的数组中保留对敌人实体的引用，可以更轻松地在后续操作（例如生成敌人）中对它们进行处理，如 代码清单 8-12 所示。

***代码清单 8-12.***  生成敌人

```
-(void) spawnEnemyOfType:(EnemyTypes)enemyType
{
    NSMutableArray* enemiesOfType = [enemies objectAtIndex:enemyType];
    for (Enemy* enemy in enemiesOfType)
    {
     // 找到第一个空闲的敌人并重新生成它
     if (enemy.visible == NO)
     {
     CCLOG(@"spawn enemy type %i", enemyType);
     [enemy spawn];
     break;
     }
    }
}

-(void) update:(ccTime)delta
{
    updateCount++;

for (int i = (EnemyType_MAX – 1); i > = 0; i--)
    {
     int spawnFrequency = [Enemy getSpawnFrequencyForEnemyType:i];
     if (updateCount % spawnFrequency == 0)
     {
     [self spawnEnemyOfType:i];
     break;
     }
    }
}
```

`update` 方法递增一个简单的 `update` 计数器。它没有考虑实际经过的时间，但由于变化通常很小，因此为了简化操作，这是一个公平的权衡。这个 `for` 循环奇特地以 `(EnemyType_MAX – 1)` 开始，并一直运行到 `i` 为负数。这样做的唯一目的是让编号较高的 `EnemyTypes` 比编号较低的 `EnemyTypes` 拥有生成优先级。如果 Boss 怪物和巡洋舰计划在同一时间出现，那么 Boss 会先生成。否则，巡洋舰可能会在试图同时生成时占用了 Boss 的生成位置，从而阻止 Boss 的生成。这是生成逻辑的一个副作用，我把它留给你来扩展和改进这段代码，因为如果你决定自己编写一个经典的竖版射击游戏版本，你可能无论如何都需要这样做。

`spawnFrequency` 是通过 `Enemy` 的 `getSpawnFrequencyForEnemyType` 方法获取的：

```
+(int) getSpawnFrequencyForEnemyType:(EnemyTypes)enemyType
{
    NSAssert(enemyType < EnemyType_MAX, @"invalid enemy type");
    NSNumber* number = [spawnFrequency objectAtIndex:enemyType];
    return number.intValue;
}
```

首先，该方法断言 `enemyType` 的数值确实在定义的范围内。然后，获取该敌人类型的 `NSNumber` 对象，并作为 `intValue` 返回。

取模运算符 `%` 返回 `updateCount` 和 `spawnFrequency` 两个操作数相除后的余数。这意味着只有当 `updateCount` 能被 `spawnFrequency` 整除（余数为 0）时，才会生成一个敌人。


```markdown

`spawnEnemyOfType`方法接着从`enemies NSMutableArray`中获取`NSMutableArray`，该数组包含`enemiesOfType`（另一个`NSMutableArray`）的列表。现在你可以仅遍历所需的敌人类型，而无需遍历添加到`CCSpriteBatchNode`的所有精灵。一旦发现某个敌人不可见，就会调用其`spawn`方法。如果该类型的所有敌人都可见，则当前屏幕上已达到最大敌人数量，不会生成更多敌人，从而有效限制了每个时刻屏幕上特定类型敌人的数量。

最后，为了让敌人出现在屏幕上，打开`GameLayer.m`文件，并在`init`方法中扩展初始化`EnemyCache`的代码。将此代码添加在`BulletCache`初始化代码的上方，别忘了在`GameLayer.m`文件顶部导入`EnemyCache.h`头文件：

```
#import "EnemyCache.h
. . .
-(id) init
{
    if ((self = [super init]))
    {
     . . .

EnemyCache* enemyCache = [EnemyCache node];
     [self addChild:enemyCache z:0];

BulletCache* bulletCache = [BulletCache node];
     [self addChild:bulletCache z:1 tag:GameSceneNodeTagBulletCache];
    }
    return self;
}
```

## 组件类

组件类旨在作为游戏逻辑的插件。如果为节点添加了组件，该节点将执行组件的行为：移动、射击、动画、显示生命条等。其主要优点是你可以将这些组件编程为通用组件，并用于大量游戏对象。组件与其`parent`交互，并应尽可能少地假设父类。当然，在某些情况下，组件要求父类是`CCSprite`甚至`Enemy`类，但你仍然可以将其用于任何类型的`CCSprite`或`Enemy`类。

你也可以根据使用组件的类来配置组件类。作为组件类的示例，请查看`Enemy`类中的`StandardShootComponent`初始化：

```
StandardShootComponent* shootComponent = [StandardShootComponent node];
shootComponent.shootFrequency = shootFrequency;
shootComponent.bulletFrameName = bulletFrameName;
[self addChild:shootComponent];
```

变量`shootFrequency`和`bulletFrameName`此前已根据`EnemyType`设置。通过将`StandardShootComponent`添加到`Enemy`类，敌人将以特定间隔发射特定子弹。由于此组件不假设父类，你甚至可以将其实例添加到`Ship`类，让玩家飞船自动以特定间隔射击！或者，仅通过激活和停用专门的射击组件，你就可以用很少的代码实现玩家更换武器的效果。你只需独立编写射击代码，然后将其添加到节点并添加一些参数。切换武器的编程逻辑只剩下何时停用哪些组件。

此外，你可以在其他有意义的游戏中重用这些组件，只需将它们添加到新项目中即可。无需修改组件类的行为，因此组件非常适合编写可重用代码，并且是许多许多游戏引擎中的标准机制。要了解更多关于游戏组件的信息，请参阅我网站的这篇博客文章：`www.learn-cocos2d.com/2010/06/prefer-composition-inheritance`。

查看`StandardShootComponent`的源代码，从头文件开始：

```
#import < Foundation/Foundation.h>
#import "cocos2d.h"

@interface StandardShootComponent : CCSprite
{
    float updateCount;
    float shootFrequency;
    NSString* bulletFrameName;
}

@property (nonatomic) float shootFrequency;
@property (nonatomic) NSString* bulletFrameName;

@end
```

关于此类有一个值得注意的地方。`StandardShootComponent`继承自`CCSprite`，尽管它不使用任何纹理，也不需要以任何方式显示在屏幕上。这是一种变通方法，因为所有`Enemy`对象都被添加到一个`CCSpriteBatchNode`中，而该节点只能包含基于`CCSprite`的对象。这也扩展到`Enemy`类的任何子节点；因此，`StandardShootComponent`需要继承自`CCSprite`，以满足`CCSpriteBatchNode`的要求。

`StandardShootComponent`的实现如列表 8-13 所示。

**列表 8-13.**  `StandardShootComponent`的完整实现

```
#import "StandardShootComponent.h"
#import "BulletCache.h"
#import "GameLayer.h"

@implementation StandardShootComponent

@synthesize shootFrequency;
@synthesize bulletFrameName;

-(id) init
{
    if ((self = [super init]))
    {
     [self scheduleUpdate];
    }

return self;
}

-(void) update:(ccTime)delta
{
    if (self.parent.visible)
    {
     updateCount + = delta;

if (updateCount > = shootFrequency)
     {
     updateCount = 0;

GameLayer* game = [GameLayer sharedGameLayer];
     CGPoint startPos = ccpSub(self.parent.position, ←
     CGPointMake(self.parent.contentSize.width * 0.5f, 0));
     [game.bulletCache shootBulletFrom:startPos
     velocity:CGPointMake(−200, 0)
     frameName:bulletFrameName];
     }
    }
}
@end
```

实际的射击代码首先检查父节点是否可见，因为如果父节点不可见，代码显然不应射击。`BulletCache`负责发射子弹，使用提供给组件的`bulletFrameName`和固定速度。对于起始位置，组件自身的位置并不重要。相反，使用父节点位置和`contentSize`属性来计算正确的起始位置——在本例中，位于敌人精灵的左侧。

这个子弹`startPos`对普通敌人效果不错，但可能需要为 Boss 敌人进行调整。我将由你自行为此组件添加另一个属性来设置子弹`startPosition`。或者，你也可以创建一个独立的`BossShootComponent`，仅将其添加到 Boss 敌人上，以创建更复杂的射击模式。`StandardMoveComponents`也是如此，对于 Boss，它可能需要悬停在屏幕右侧的某个位置。

说到这个，`StandardMoveComponent`的接口非常轻量：

```
#import < Foundation/Foundation.h>
#import "cocos2d.h"

@interface StandardMoveComponent : CCSprite
{
    CGPoint velocity;
}
@end
```

其实现也并不复杂，如列表 8-14 所示。

**列表 8-14.**  `StandardMoveComponent`的实现

```
#import"StandardMoveComponent.h"
#import "GameLayer.h"

@implementation StandardMoveComponent

-(id) init
{
    if ((self = [super init]))
    {
     velocity = CGPointMake(−100, 0);
     [self scheduleUpdate];
    }

return self;
}

-(void) update:(ccTime)delta
{
    if (self.parent.visible)
    {
     CGSize screenSize = [CCDirector sharedDirector].winSize;
     if (self.parent.position.x > screenSize.width * 0.5f)
     {
     self.parent.position = ccpAdd(self.parent.position, ←
     ccpMult(velocity, delta));
     }
    }
}
@end
```

`StandardMoveComponent`初始化其`velocity`，使得无论哪个节点使用它，都会以固定速度向左移动。`update`方法首先检查父节点是否可见，因为如果不可见，则表示父节点当前处于非活动状态。父节点会一直移动，直到以给定的`velocity`乘以`delta`时间越过屏幕中心位置，从而确保移动距离不受帧率影响。

```


