# 排版后的内容

如你所见，组件代码通常非常简单。这也让查找和修复组件中的缺陷变得容易。随着时间的推移，你可以积累大量此类组件以便在其他应用中复用，从而减少编写通用代码的数量，将更多精力投入到应用程序特有的代码上——那些新颖、富有挑战性且充满趣味的新代码。

## 射击目标

我差点忘了——你其实是想射击敌人，对吧？好吧，在 `ShootEmUp03` 项目中，你就能实现这个目标！

检查子弹是否命中目标的最佳起点在 `BulletCache` 类中。我已经添加了完成此任务的方法。实际上，我添加了三个方法，其中两个是公开的；第三个是类私有方法，用于合并通用代码（见清单 8-15）。

**清单 8-15.**  检测子弹碰撞

```
-(BOOL) isPlayerBulletCollidingWithRect:(CGRect)rect
{
    return [self isBulletCollidingWithRect:rect usePlayerBullets:YES];
}

-(BOOL) isEnemyBulletCollidingWithRect:(CGRect)rect
{
    return [self isBulletCollidingWithRect:rect usePlayerBullets:NO];
}

-(BOOL) isBulletCollidingWithRect:(CGRect)rect usePlayerBullets:(bool)usePlayerBullets
{
    BOOL isColliding = NO;

for (Bullet* bullet in batch.children)
    {
     if (bullet.visible && usePlayerBullets == bullet.isPlayerBullet)
     {
     if (CGRectIntersectsRect([bullet boundingBox], rect))
     {
         isColliding = YES;

// 移除子弹
         bullet.visible = NO;
         break;
      }
     }
    }
    return isColliding;
}
```

你还需要将方法声明添加到 `BulletCache` 接口中：

```
-(BOOL) isPlayerBulletCollidingWithRect:(CGRect)rect;
-(BOOL) isEnemyBulletCollidingWithRect:(CGRect)rect;
```

使用 `isPlayerBulletCollidingWithRect` 和 `isEnemyBulletCollidingWithRect` 这两个封装方法的理念，在于隐藏用于碰撞检测的子弹类型判断的内部细节。你当然可以将 `usePlayerBullets` 参数暴露给其他类，但这样做只会让将来将参数从 `BOOL` 类型改为 `enum` 类型变得更加困难，假如你想引入第三种子弹类型的话。

当然，只有可见的子弹才能发生碰撞，并且通过检查子弹新增的 `isPlayerBullet` 属性，可以确保敌人不会误伤自己。实际的碰撞检测是一个简单的 `CGRectIntersectsRect` 测试，如果子弹确实命中了目标，该子弹也会被设为不可见，从而使其消失。`Bullet` 类已被扩展，增加了 `isPlayerBullet` 属性：

```
#import <Foundation/Foundation.h>
#import "cocos2d.h"

@class Ship;

@interface Bullet : CCSprite
{
    CGPoint velocity;
    float outsideScreen;
    BOOL isPlayerBullet;
}

@property (readwrite, nonatomic) CGPoint velocity;
@property (readwrite, nonatomic) BOOL isPlayerBullet;

+(id) bullet;
-(void) shootBulletFrom:(CGPoint)startPosition
     velocity:(CGPoint)vel
     frameName:(NSString*)frameName
     isPlayerBullet:(BOOL)playerBullet;
@end
```

相应地，`Bullet` 类实现中的 `shootBulletFrom` 方法也已扩展，能够接收 `isPlayerBullet` 参数：

```
@synthesize velocity, isPlayerBullet;

. . .

-(void) shootBulletFrom:(CGPoint)startPosition
     velocity:(CGPoint)vel
     frameName:(NSString*)frameName
     isPlayerBullet:(BOOL)playerBullet;
{
    self.velocity = vel;
    self.position = startPosition;
    self.visible = YES;
    self.isPlayerBullet = playerBullet;
    . . .
}
```

与此完全相同，`BulletCache` 类中的 `shootBulletFrom` 方法也需要扩展，以接收 `isPlayerBullet` 参数，并将其传递给 `Bullet` 类。`InputLayer` 和 `StandardShootComponent` 都使用了 `shootBulletFrom` 方法，它们需要传入 `isPlayerBullet` 参数。`InputLayer` 中的调用将其设为 `YES`，因为该代码负责通过飞船进行射击。`StandardShootComponent` 则将 `isPlayerBullet` 设为 `NO`，因为该组件用于敌人。

持有所有 `Enemy` 对象的 `EnemyCache` 类是调用新的碰撞检测方法来检查是否有敌人被玩家子弹击中的理想位置。该类新增了一个 `checkForBulletCollisions` 方法，该方法在其 `update` 方法中被调用，如清单 8-16 所示。

**清单 8-16.**  在 EnemyCache 类中执行碰撞检测

```
#import "BulletCache.h"
#import "GameLayer.h"

. . .
-(void) update:(ccTime)delta
{
    . . .

[self checkForBulletCollisions];
}

-(void) checkForBulletCollisions
{
    for (Enemy* enemy in batch.children)
    {
        if (enemy.visible)
        {
            BulletCache* bulletCache = [GameLayer sharedGameLayer].bulletCache;
            CGRect bbox = enemy.boundingBox;
            if ([bulletCache isPlayerBulletCollidingWithRect:bbox])
            {
                // 这个敌人被击中了...
                [enemy gotHit];
            }
        }
    }
}
```

在这里，能够遍历游戏中所有敌人物体并跳过那些当前不可见的敌人，再次体现了便利性。利用每个敌人的 `boundingBox` 与 `BulletCache` 的 `isPlayerBulletCollidingWithRect` 方法进行检测，你可以快速判断是否有敌人被玩家子弹击中。如果是，就会调用 `Enemy` 的 `gotHit` 方法，该方法目前仅在敌人没有更多剩余生命值时将其设为不可见：

```
-(void) gotHit
{
    hitPoints--;
    if (hitPoints < = 0)
    {
        self.visible = NO;
    }
}
```

请将其视为一个练习：由你来实现玩家的飞船被敌人子弹击中的功能。你需要在 `Ship` 中安排一个 `update` 方法，然后实现 `checkForBulletCollisions` 方法，并在 `update` 方法中调用它。你需要将对 `isPlayerBulletCollidingWithRect` 的调用改为 `isEnemyBulletCollidingWithRect`，并决定在被子弹击中时如何响应——例如播放音效，或者显示“游戏结束”消息。

## 为 Boss 添加生命值条

Boss 怪物并非一击就能轻松击败的。它应该通过显示一条会随着每次攻击而减少的生命值条，为玩家提供关于其生命状态的视觉反馈。创建生命值条的第一步是使用 `Enemy` 类中的 `hitPoints` 成员变量，该变量指示敌人在被摧毁前能承受多少次攻击。`initialHitPoints` 变量则保存最大生命值，因为在敌人被消灭后，你需要能够恢复其原始生命值。这两个变量你都会用到。

要显示生命值条，另一个组件类是最佳选择，因为它提供了即插即用的解决方案，并且在需要时可以复用于其他敌人。`HealthbarComponent` 的头文件非常简单：

```
#import < Foundation/Foundation.h>
#import "cocos2d.h"

@interface HealthbarComponent : CCSprite
{
}

-(void) reset;

@end
```

`HealthbarComponent` 类的实现则更有趣，如清单 8-17 所示。

**清单 8-17.**  HealthBarComponent 根据敌人剩余生命值更新其 scaleX 属性

```
#import"HealthbarComponent.h"
#import "Enemy.h"

@implementation HealthbarComponent

-(void) onEnter
{
    [super onEnter];
    [self scheduleUpdate];
}
```


