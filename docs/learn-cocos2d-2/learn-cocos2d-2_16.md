# PVRTC2 和 PVRTC4 图像格式  

`PVRTC2` 和 `PVRTC4` 图像格式分别提供每像素 2 位和 4 位的色深，且不带 Alpha 通道。仅在处理单调或深色背景图像，且确实需要节省内存和提升渲染速度时才使用这些格式，因为它们会严重影响图像质量。你可以将其想象为低质量设置下 JPEG 图像中出现的伪影。  

`抖动`选项允许你在图像格式需要降低颜色深度时优化图像质量。抖动通过将色调相近的像素随机分布到更大区域来模拟渐变效果。这能有效减少图像颜色深度降低时出现的“条带”现象。由于所有抖动选项都在`TexturePacker`预览中实时应用，不会影响源图像，因此你只需尝试不同的抖动算法，找到效果最佳的那一个。  

**提示：** 在评估抖动算法时，请记住最终的质量测试当然是在设备上运行你的游戏。某些在电脑屏幕上清晰可见的伪影在设备上可能不明显。这尤其因为电脑屏幕的色彩配置文件与设备不同——可能是由于手动调节（亮度、对比度、色调）、显示技术的限制，或屏幕老化导致的色彩鲜艳度变化。这也意味着单台设备的显示效果不能代表游戏的最终外观。你至少应将设备亮度分别设为最低和最高级别进行测试。  

完成`输出`设置后，只需点击`发布`，`TexturePacker` 就会将高清和标清纹理及对应的`plist`文件写入你的`Resource/es`文件夹。  

## 在 cocos2d 中使用纹理图集  

接下来，你应该将新纹理图集添加到 Xcode 项目的`Resources`组中。`Cocos2d` 只需要纹理图集的 `game-art-hd.pvr.ccz`、`game-art.pvr.ccz`、`game-art-hd.plist` 和 `game-art.plist` 文件。不要将 `TexturePacker` 的 `.tps` 文件和单个源图像文件添加到项目中。代码清单 6-13 中的代码将替换代码清单 6-11 中的代码。  

### **代码清单 6-13.** Ship 类现在使用纹理图集作为初始帧和动画  

```
// 加载纹理图集的精灵帧；同时会加载同名纹理
CCSpriteFrameCache* frameCache = [CCSpriteFrameCache sharedSpriteFrameCache];
[frameCache addSpriteFramesWithFile:@"game-art.plist"];

// 使用精灵帧名称（例如文件名）加载飞船精灵
if ((self = [super initWithSpriteFrameName:@"ship.png"]))
{
   // 加载飞船动画帧
   NSMutableArray* frames = [NSMutableArray arrayWithCapacity:5];
   for (int i = 0; i < 5; i++)
   {
        NSString* file = [NSString stringWithFormat:@"ship-anim%i.png", i];

CCSpriteFrame* frame = [frameCache spriteFrameByName:file];
        [frames addObject:frame];
   }

// 从所有精灵动画帧创建动画对象
   CCAnimation* anim = [CCAnimation animationWithSpriteFrames:frames delay:0.08f];

// 使用 CCAnimate 动作运行动画
   CCAnimate* animate = [CCAnimate actionWithAnimation:anim];
   CCRepeatForever* repeat = [CCRepeatForever actionWithAction:animate];
   [self runAction:repeat];
}
```

在代码的最开始，我将 `sharedSpriteFrameCache` 赋值给了局部变量。这样做的唯一原因是 `[CCSpriteFrameCache sharedSpriteFrameCache]` 单例访问器写起来太长了。  

要加载纹理图集，请使用 `CCSpriteFrameCache` 的方法 `addSpriteFramesWithFile`，并传入该纹理图集的 `.plist` 文件名。`CCSpriteFrameCache` 会加载精灵帧，并会尝试加载纹理。`Cocos2d` 会自动在 Retina 显示屏设备上尝试加载带有 `-hd` 后缀的文件。  

**注意：** 如果你使用大型纹理图集（尺寸为 1024×1024 或更大），应在游戏开始前加载此纹理。加载如此大的纹理需要一些时间（最坏情况下会导致游戏卡顿几秒钟）。在 Retina iPad 上测试时，你可能注意不到延迟，但使用 iPhone 3GS 的用户肯定会注意到。  

由于 `Ship` 类继承自 `CCSprite`，并且我希望它使用纹理图集中的 `ship.png` 图像，因此我将其初始化改为使用 `initWithSpriteFrameName` 方法。这与从纹理图集中使用精灵帧名称初始化普通 `CCSprite` 的代码相同。  

```
CCSprite* sprite = [CCSprite spriteWithSpriteFrameName:@"ship.png"];
```

如果你加载了多个纹理图集，且只有一个包含名为 `ship.png` 的精灵帧，`cocos2d` 仍会找到该帧并为精灵使用正确的纹理。本质上，你可以按名称使用精灵帧，就像它们是图像文件名一样，但无需知道哪个纹理包含实际图像（当然，除非你使用 `CCSpriteBatchNode`，它要求所有子节点使用相同纹理）。  

在代码清单 6-13 中，你可以去除初始化 `CCSpriteFrame` 对象所需的大部分额外代码。不再需要加载 `Texture2D` 并定义纹理尺寸。取而代之的是，只需调用 `[CCSpriteFrame spriteFrameByName:file]` 来创建具有对应名称的精灵帧。  

## 更新 CCAnimation 辅助类别  

尽管使用纹理图集可以显著减少创建 `CCAnimation` 的代码，但将这些代码封装到 `CCAnimationHelper` 类中仍然值得。毕竟，一行代码仍然比五行代码少，尤其是在其他地方也要使用相同五行代码的情况下。闲言少叙，代码清单 6-14 展示了扩展后的 `CCAnimation` 辅助类别接口声明，其中添加了 `animationWithFrame` 方法。  

### **代码清单 6-14.** CCAnimation 辅助类别的 @interface  

```
interface CCAnimation (Helper)
+(CCAnimation*) animationWithFile:(NSString*)name
                        frameCount:(int)frameCount
                             delay:(float)delay;

+(CCAnimation*) animationWithFrame:(NSString*)frame
                        frameCount:(int)frameCount
                             delay:(float)delay;
@end
```

这段代码本质上使用了相同参数的同名方法，只不过此方法使用精灵帧而非文件名。其实现并不复杂，与代码清单 6-15 中所示的 `animationWithFile` 方法非常相似。  

### **代码清单 6-15.** animationWithFrame 辅助方法使创建动画更简单  

```
// 从精灵帧创建动画
+(CCAnimation*) animationWithFrame:(NSString*)frame
                        frameCount:(int)frameCount
                             delay:(float)delay
{
  // 加载飞船动画帧作为纹理并创建精灵帧
  NSMutableArray* frames = [NSMutableArray arrayWithCapacity:frameCount];
  for (int i = 0; i < frameCount; i++)
  {
      NSString* file = [NSString stringWithFormat:@"%@%i.png", frame, i];
      CCSpriteFrameCache* frameCache = [CCSpriteFrameCache sharedSpriteFrameCache];
      CCSpriteFrame* frame = [frameCache spriteFrameByName:file];
      [frames addObject:frame];
  }
```  



```objectivec
// 从所有精灵动画帧中返回一个动画对象
return [CCAnimation animationWithSpriteFrames:frames delay:delay];
```

现在，最大的好处是，你可以仅用一行代码，通过精灵帧名称从纹理图集中创建动画：

```objectivec
// 从所有精灵动画帧中创建动画对象
CCAnimation* anim = [CCAnimation animationWithFrame:@"ship-anim"
                    frameCount:5
                    delay:0.08f];
```

不过，更更更大的好处在于，你现在可以将动画作为单独的文件进行处理，稍后再创建纹理图集。你只需要将一行代码从使用 `animationWithFile` 方法改为 `animationWithFrame` 方法即可。这使得你可以使用单个文件快速构建动画原型，只有在满意之后，才将动画帧打包到纹理图集中，并从中加载动画图像。

你还会在 `Sprites02` 项目中找到这个更新的 `CCAnimationHelper` 代码。

## 合而为一，一即所有

只要可能，你应该将游戏中的所有图像都放入一个纹理图集中，或者尽可能少地使用图集，并且最好使用一个 `CCSpriteBatchNode` 来绘制来自同一纹理图集的所有精灵。从工作流程和性能角度来看，使用一个尺寸为 `2048×2048` 的纹理图集，比使用 10 个较小的图集更为高效，而且你将不得不使用至少 10 个精灵批处理节点，而不是仅仅一个。这意味着至少 10 次绘制调用，这并不理想。组织图像可能需要一些思考，但这是非常值得的。

与代码不同（你应该将代码分离成不同的逻辑组件），对于纹理图集，你的目标应该是将尽可能多的图像放入同一个纹理图集，同时尽可能减少每个纹理图集中浪费的空间。

为玩家图像使用一个纹理图集，为怪物 A、B、C 及其动画使用另一个纹理图集，等等，这看起来可能合乎逻辑。但这意味着更多的绘制调用。然而，只有当你每个游戏对象都有大量图像，并且你想在任意时刻有选择地加载哪些图像到内存中时，这才有帮助。其中一个场景可能是一款包含不同世界的射击游戏，你知道每个世界都有不同类型的敌人。在这种情况下，将不同世界的敌人混合到同一个纹理图集中是没有意义的。否则，仅仅为了组织方便，你不应该按游戏对象来拆分纹理图集，而应该尽可能填满每个纹理图集。

不要养成每个图形对象都创建一个纹理图集的习惯，即使这看起来合乎逻辑。性能最佳的纹理图集是满载着种类繁多的图像，这些图像都可以通过单个精灵批处理节点进行渲染。

只要你的游戏图像可以放入两到三个尺寸为 `2048×2048` 的纹理图集中，你就应该将所有图像放入这些纹理图集并预先加载。这将为你的纹理占用 32MB 到 48MB 的内存。你的实际程序代码和其他资源（如音频文件）不会占用那么多空间，因此即使在只有 256MB 内存的 iOS 设备上，你也应该能够将这些纹理图集保留在内存中。

然而，一旦超过这个点，你就需要一种更好的策略来管理纹理内存。如前所述，一种策略是将游戏图像按世界划分，只加载当前世界所需的纹理图集。这会在加载新世界时产生短暂的延迟，并且非常适合使用第 5 章中描述的 `LoadingScene`。

由于 cocos2d 会自动缓存所有图像，你需要一种方法来专门卸载你知道不需要的纹理。你可以依赖 cocos2d 来为你执行此操作：

```objectivec
[[CCSpriteFrameCache sharedSpriteFrameCache] removeUnusedSpriteFrames];
[[CCTextureCache sharedTextureCache] removeUnusedTextures];
```

显然，你应该只在想要移除未使用纹理时调用这些方法。通常你会在切换场景后执行此操作，而不是在游戏过程中。请记住，切换场景会导致前一个场景在新场景初始化完成后才被释放。这意味着你不能在场景的 `init` 方法中使用 `removeUnused` 方法，因为纹理仍在使用中——除非你使用第 5 章中的 `LoadingScene` 在两个场景之间进行过渡，在这种情况下，你应该扩展它，使其在替换为新场景之前移除未使用的纹理。

如果你绝对想在加载新纹理之前从内存中移除所有纹理，则应改用清除方法：

```objectivec
[CCSpriteFrameCache purgeSharedSpriteFrameCache];
[CCTextureCache purgeSharedTextureCache];
```

## 总结

在本章中，你学习了如何使用 `CCSpriteBatchNode` 来更快速地渲染使用相同纹理的多个精灵，无论该纹理是单个图像还是纹理图集的一个精灵帧。

从 `CCSprite` 继承你的游戏对象也会带来一些细微的差异和障碍，我在本章前面已经演示过这些，接着又向你展示了如何创建精灵动画。由于创建动画的代码非常复杂，我以 `CCAnimationHelper` 分类的形式为你提供了解决方案。

我还向你展示了如何使用纹理图集，以及为什么要使用以及如何使用它们。当然，一提到“纹理图集”，就不可能不提到 TexturePacker。它无疑是创建和修改纹理图集的最佳工具，如果你不想花钱购买，仍然可以使用没有高级功能的免费命令行版本。

在下一章中，你将着手制作下一款游戏，我们将努力让这款射击游戏变得可玩。

## 第 7 章

