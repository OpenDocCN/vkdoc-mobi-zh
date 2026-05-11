# 第 8 章：接触与碰撞

### 168

在 `SKBRatz.m` 文件中现有的 `ratzKnockedOut` 方法之后插入该方法的实现：

```objc
[self runRight];
}
}];
}

- (void)ratzCollected:(SKScene *)whichScene
{
    NSLog(@"%@ collected", self.name);
}

#pragma mark 移动
```

在 `SKBPlayer.m` 文件的 `initNewPlayer` 方法中添加一个新的接触位掩码：

```objc
player.physicsBody.categoryBitMask = kPlayerCategory;
player.physicsBody.contactTestBitMask = kWallCategory | kLedgeCategory |
                                        kCoinCategory | kRatzCategory;
player.physicsBody.collisionBitMask = kBaseCategory | kWallCategory |
                                      kLedgeCategory ;
```

在 `SKBGameScene.m` 文件的 `didBeginContact` 方法中，于现有的玩家/金币检测部分之后插入接触检测的 `if()` 语句：

```objc
// 奖励一些分数
_playerScore = _playerScore + kCoinPointValue;
[_scoreDisplay updateScore:self newScore:_playerScore];
}

// 玩家 / 老鼠
if ((((firstBody.categoryBitMask & kPlayerCategory) != 0) &&
     ((secondBody.categoryBitMask & kRatzCategory) != 0))) {
    SKBRatz *theRatz = (SKBRatz *)secondBody.node;
    if (theRatz.ratzStatus == SBRatzKOfacingLeft || theRatz.ratzStatus ==
        SBRatzKOfacingRight) {
        [theRatz ratzCollected:self];
    }
    // 奖励一些分数
    _playerScore = _playerScore + kRatzPointValue;
    [_scoreDisplay updateScore:self newScore:_playerScore];
}
// 老鼠 / 壁架
```

构建并运行程序，验证当玩家跑过一个被击倒的敌人时，检测是否返回肯定结果。现在，你可以为检测为真的情况“增添花样”了。

[www.it-ebooks.info](http://www.it-ebooks.info/)

