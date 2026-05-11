# 第 2 章：SKActions 和 SKTextures：你的第一个动画精灵

**注意** 务必注意，数组内纹理对象的排列顺序决定了最终的帧序列。此外，你可以重复使用对象。例如，你可以这样写：

`NSArray *textureArray = @[f2,f1,f2,f3,f1,f4];`

这样一来，四个图像帧会按照该顺序显示，在动画的每次循环中，屏幕上将绘制总计六帧。

`_playerSprite = [SKSpriteNode spriteNodeWithTexture:f1];`

`_playerSprite.position = location;`

接着你创建了 `SKSpriteNode`（使用你在接口文件中通过 `@property` 语句创建的实例变量），并只应用了其中一个纹理。具体使用哪一个并不重要，但使用第一个动画帧是合理的。

`SKAction *runRightAction = [SKAction animateWithTextures:textureArray timePerFrame:0.1]; SKAction *runForever = [SKAction repeatActionForever:runRightAction];` 这里你使用数组中的完整纹理集创建了一个 `SKAction`，并将每一帧设置为每 0.1 秒绘制一次。接着你创建了另一个 `SKAction`，使其无限重复该单个动作。

`[_playerSprite runAction:runForever];`

`[self addChild:_playerSprite];`

这部分代码将无限重复的动作添加到精灵上，最后将精灵添加到场景中。

## 总结

本章旨在帮助你更深入地理解 `SKSpriteNodes`、`SKTextures` 和 `SKActions` 在游戏中创建视觉组件时所扮演的角色。如果没有任何可视且可交互的内容，那游戏也就谈不上是游戏，对吧？精灵及其相关动作是每款游戏的基础核心元素。在后续章节中，当你为游戏添加更多组件时，将继续使用这些对象。

在本章中，你还添加了第二个 `SKScene`，并初步了解了 `SKTransitions`，它能在场景切换时添加一些不错的视觉特效。

在下一章中，我将为奔跑动画添加精灵的移动。你不能让角色只在原地奔跑而不移动！你将实现用户触摸事件来判定角色的移动方向，并增加跳跃能力。在此过程中，我将向你介绍 Sprite Kit 自带的物理引擎，它将为你的游戏设计增添强大的功能。物理引擎的额外好处是，你无需编写所有代码来处理游戏环境中的物理约束和反应。

[www.it-ebooks.info](http://www.it-ebooks.info/)

