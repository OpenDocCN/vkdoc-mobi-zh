# 还有第三个值（`SKView` 的属性），此处虽未使用，但你随时可以添加：`showsDrawCount`。当使用 `SKEffectNode` 对象时，该值会很有用，我们将在第 9 章后面有机会用到它。在分析游戏性能时，可将此绘制次数作为另一项参考数据。

除了这些调试选项外，你无需在此文件中添加或修改任何代码。

接下来选择 `SKBMyScene.m` 文件（见图 1-9）。

***图 1-9.** 在编辑器中打开的 `SKBMyScene.m` 文件*

这里才是实用代码的主体。运行游戏时你所看到的一切都发生在这里：“你好，世界！”标签、调试信息、飞船的生成位置以及旋转动作的启动——所有这些都发生在用户触摸 iPhone 屏幕，以及你在模拟器屏幕上点击的那一刻。两个简短的方法就完成了这么多操作，你不觉得吗？

让我们更详细地审视一下。首先，看看 `initWithSize` 方法。

```
self.backgroundColor = [SKColor colorWithRed:0.15 green:0.15 blue:0.3 alpha:1.0];
```

这里，你使用 RGB（颜色）和 alpha（透明度）值设置了背景色。

```
SKLabelNode *myLabel = [SKLabelNode labelNodeWithFontNamed:@"Chalkduster"];
myLabel.text = @"Hello, World!";
myLabel.fontSize = 30;
myLabel.position = CGPointMake(CGRectGetMidX(self.frame), CGRectGetMidY(self.frame));
```

然后你创建了一个 `SKLabelNode` 对象，并设置了一些重要属性，比如字体、大小、位置以及文本内容。在计算位置时，你可能注意到使用了便捷方法（`CGPointMake` 和 `GetMidX`），通过 `SKScene` 的 `frame` 属性来确定屏幕中心。

```
[self addChild:myLabel];
```

最后，你将创建的 `SKLabelNode` 作为子节点添加到 `SKScene` 中。这就是精灵或节点添加到屏幕上的方式：通过 `SKScene` 对象的 `addChild` 方法。

你可能已经注意到，所有这些操作都发生在场景初始化过程中。换句话说，在应用启动后立即发生，因为当故事板加载时，这个场景就会被实例化。因此，这个方法成为添加代码的最佳位置，用于处理用户启动游戏时你想要发生的任何事情。你可能想要显示某种启动画面，或者直接进入主题，向用户展示几个可选按钮：例如单人游戏或双人游戏。

最后，是 `touchesBeganWithEvent` 方法。正如其名，当用户将手指放在屏幕上时会调用此方法。而不是当手指离开屏幕时（那是 `touchesEndedWithEvent` 方法），例如在使用按钮时常用的方法。这允许用户在按钮之间做出选择时改变主意。但那是另一本书要讨论的不同话题了。在这个游戏中，当用户触摸屏幕时，你不希望延迟即时操作，因此这是选择方法的更好选择。

`for` 循环用于识别并处理多个同时发生的触摸。这在模拟器上有点棘手。然而，在真实设备上，尝试同时按下两根手指，你会看到预期的效果：在你手指放置的位置出现两艘飞船。

```
CGPoint location = [touch locationInNode:self];
```

对于每个触摸事件，你首先确定触摸的位置。

```
SKSpriteNode *sprite = [SKSpriteNode spriteNodeWithImageNamed:@"Spaceship"];
```

然后，通过创建一个新的 `SKSpriteNode` 对象来创建一个新精灵。为此，你可以将已添加到项目中的图片文件名传递给 `SKSpriteNode` 的便捷方法，瞧，你就得到了一个精灵。不过它现在还不会显示在屏幕上。那是后面的事。注意，项目导航器左侧已经有一个名为 `Spaceship.png` 的图片文件。点击它可以在 Xcode 中查看。

```
sprite.position = location;
```

你将精灵的位置设置为你从用户触摸点确定的位置。有趣的是，如果我们跳过或注释掉接下来的两行代码，飞船会出现在屏幕上，但不会旋转。

```
SKAction *action = [SKAction rotateByAngle:M_PI duration:1];
```

这里你创建了一个名为 `SKAction` 的新对象。这种类型的对象将以多种不同的方式进行配置，你将在后续章节中了解其中一些。一旦创建并配置完成，它就会被关联或应用于一个精灵（`SKSpriteNode`），从而将该动作应用于该精灵。这是对一个相当直接过程的冗长描述。

在这个例子中，你创建了一个旋转动作。本质上，这些参数的意思是“在一秒内旋转 180 度”。`rotateByAngle` 参数要求以弧度而非度数提供角度，`M_PI` 是 `math.h` 库中提供的一个 C 类型函数，返回 π 的值，弧度制下代表半圆。`duration` 参数期望传入的值以秒为单位。

```
[sprite runAction:[SKAction repeatActionForever:action]];
```

现在你有了一个配置好的动作，这行代码将动作附加到精灵上。不仅如此，它还使其无限重复，而不是仅执行一次完整的半圆旋转（180 度）。如果你只想让它旋转一次然后停止，可以改为：

```
[sprite runAction:action];
```

然后你将配置好的精灵添加到场景中，这实际上将其添加到屏幕上：

```
[self addChild:sprite];
```

## 总结

就是这样！哇！你看到完成所有这些操作并不需要太多代码吗？回顾几年以前，你意识到完成同样的事情需要多少代码吗？更不用说仅用那么大小的飞船，性能会有多差了？iPhone 不仅在游戏处理能力上令人惊叹，而且随着 Sprite Kit 的发布，你创作下一款热门游戏的工作变得如此轻松！

本章向你介绍了一些 Sprite Kit 对象以及你可以用它们做的许多事情。动画从未如此简单！准备好迎接更多内容了吗？接下来的章节将继续添加更多激动人心的功能。让我们继续前进。

## 第 2 章 SKActions 和 SKTextures：你的第一个动画精灵

“Hello World”应用程序是对一些 Sprite Kit 基础知识（如 `SKScene`、`SKLabelNode`、`SKSpriteNode` 和 `SKAction` 对象）的良好介绍，但它严重缺乏功能性（除非你认为一款伟大游戏就是比下一个开发者更快地生成 100 艘旋转飞船）！

在本章中，你将开始编写一款名为“下水道兄弟”的游戏。你将首先向用户呈现一个启动画面，当用户触摸屏幕开始游戏时，你将过渡到一个新屏幕并添加一个动画精灵。随后的每一章都会增加更多特性和功能，直到本书结尾，你将拥有一个功能完整的游戏。在此过程中，你将接触到许多 Sprite Kit 功能，最终，你应该拥有足够的知识和经验，使用 Sprite Kit 开始编写你自己的畅销 2D 游戏，并在 iOS App Store 上销售。

## 初露锋芒

为了尽可能简化流程，你将像上一章一样从一个模板开始。



这段代码为你提供了一个 `SKScene` 和初始代码，可作为构建游戏的基础骨架。

因此，与之前一样，你需要从“欢迎使用 Xcode”界面中选择“创建新 Xcode 项目”（如果你刚刚启动 Xcode）。如果 Xcode 已打开且未显示欢迎界面，则可以从 `File` 菜单中选择“新建项目”。

在“新建项目模板”表单中（参见第 1 章的图 1-1），确保左侧列表选中“iOS - 应用程序”，然后点击 `SpriteKit Game` 模板图标。最后，点击“下一步”按钮。

使用以下数据填写该表单中的字段（请参考图 1-2 以回忆具体内容）：

- **产品名称:** `Sewer Bros`
- **组织名称:** `你的名字`
- **公司标识符:** `com.yourname`
- **类前缀:** `SKB`
- **设备:** `iPhone`

下一个表单是标准的“存储”表单，你可以在此选择新项目的存储位置。做出选择后，点击“创建”按钮即可。

如果你想要验证一切是否按预期工作，可以随时构建并运行。经常这样做总是一个好习惯，以便尽早解决简单的错误和问题。如果等到大量代码更改后再构建和运行程序，那么查找可能很早就产生的难以捉摸的 bug 会变得非常痛苦。

## 移除不必要的琐碎内容

让我们回到模板提供的代码中，删除一些不需要的内容，以便最终产品尽可能精简，并在未来作为参考时易于阅读。

首先在 `SKBAppDelegate.m` 文件中，删除代码中的这五个方法（只保留名为 `application:didFinishLaunchingWithOptions:` 的方法）：

```objc
- (void)applicationWillResignActive:(UIApplication *)application
{
    // ...
}

- (void)applicationDidEnterBackground:(UIApplication *)application
{
    // ...
}

- (void)applicationWillEnterForeground:(UIApplication *)application
{
    // ...
}

- (void)applicationDidBecomeActive:(UIApplication *)application
{
    // ...
}

- (void)applicationWillTerminate:(UIApplication *)application
{
    // ...
}
```

## 设备方向

游戏将只支持一种设备方向：横屏。你可以在两个地方进行修改：

1. 在 `SKBViewController.m` 文件中，将 `supportedInterfaceOrientations` 方法修改为如下代码：

```objc
- (NSUInteger)supportedInterfaceOrientations
{
    return UIInterfaceOrientationMaskLandscape;
}
```

2. 在左侧的“项目导航器”窗口中，单击主项目文件“Sewer Bros”一次。确保选中了“Sewer Bros”目标，而不是项目本身。然后选择顶部的“通用”选项卡，以便查看和编辑“部署信息”（参见图 2-1）。

**图 2-1.** *Sewer Bros 部署信息编辑器*

取消选中“设备方向”列表中的“竖屏”复选框。现在，如果你构建并运行程序，将会看到模拟器以横屏模式启动。当你尝试旋转设备时，它只会以横屏模式正确显示和运行。

## 轻微的视图控制器更改

由于视图控制器视图默认以竖屏方式加载，因此当场景添加到视图时，大小值可能不符合预期。要解决此问题，可以在 `viewWillLayoutSubviews` 方法中设置场景及其缩放属性，而不是在通常的 `viewDidLoad` 方法中，因为在视图加载完成后已经太迟了——必须在加载之前完成。

在 `SKBViewController.m` 文件中，将 `viewDidLoad` 方法替换为以下代码：

```objc
- (void)viewWillLayoutSubviews
{
```


[toc]

### `/GKState didEnterWithPreviousState` 方法

```objc
[super viewWillLayoutSubviews];

// Configure the view.

SKView * skView = (SKView *)self.view;

if (!skView.scene) {

skView.showsFPS = YES;

skView.showsNodeCount = YES;

// Create and configure the scene.

SKScene * scene = [SKScene sceneWithSize:skView.bounds.size];

scene.scaleMode = SKSceneScaleModeAspectFill;

// Present the scene.

[skView presentScene:scene];

}

}
```

注意，你还需要添加一个`if()`语句来检查场景是否已经附加到视图上，如果场景已经存在，则忽略场景创建代码。这是因为这个方法可能被调用多次。

### 移除多余模板文本

现在你将移除模板代码中的“Hello World”文本。在`SKBMyScene.m`文件中，查看第一个`initWithSize`方法内部的代码，并特别关注`if`语句中的以下代码：

```objc
/* Set up your scene here */
self.backgroundColor = [SKColor colorWithRed:0.15 green:0.15 blue:0.3 alpha:1.0];
SKLabelNode *myLabel = [SKLabelNode labelNodeWithFontNamed:@"Chalkduster"];
myLabel.text = @"Hello, World!";
myLabel.fontSize = 30;
myLabel.position = CGPointMake(CGRectGetMidX(self.frame), CGRectGetMidY(self.frame));
[self addChild:myLabel];
```

这就是背景颜色和文本块被创建、配置并添加到场景中的地方。你将用你自己的启动画面来替换它！

### 可供下载的图像

我已为读者提供了一套完整的图像和源代码，你可以从 Apress 网站下载。这是本书页面的链接：[`www.apress.com/9781430264392`](http://www.apress.com/9781430264392)。

在页面左侧的书目资源部分寻找“Source Code”链接。解压归档文件并将项目文件夹下载到方便的位置。在此文件夹内，你可以浏览到名为“Images”的文件夹，并查找以`SewerSplash`开头的图像文件。

这些是你将要添加到游戏项目中的文件。

在 Xcode 中，从“File”菜单中选择“Add Files to 'Sewer Bros…'”。导航到`SewerSplash_480.png`文件并单击一次以选中它。然后按住 Shift 键单击第二个名为`SewerSplash_568.png`的文件，同时选中这两个文件。*确保“Destination: Copy Items into Destination's Group Folder (if Needed)”被勾选*，然后确保“Add to Targets: Sewer Bros”被勾选。单击“Add”按钮（见图 2-2）。

**Figure 2-2.** 向项目添加文件

如果成功，你将看到这些文件被添加到窗口左侧的项目导航器中。

如果你愿意，可以逐个点击它们来查看它们的外观。

### 背景颜色

在这里，你将把背景颜色设为纯黑色。`UIColor`提供了一些便捷方法，让你可以通过颜色名称快速创建常用颜色，而无需尝试确定 RGB 值。为方便你参考，我将它们列出如下：

```objc
blackColor; // 0.0 white
darkGrayColor; // 0.333 white
lightGrayColor; // 0.667 white
whiteColor; // 1.0 white
grayColor; // 0.5 white
redColor; // 1.0, 0.0, 0.0 RGB
greenColor; // 0.0, 1.0, 0.0 RGB
blueColor; // 0.0, 0.0, 1.0 RGB
cyanColor; // 0.0, 1.0, 1.0 RGB
yellowColor; // 1.0, 1.0, 0.0 RGB
magentaColor; // 1.0, 0.0, 1.0 RGB
orangeColor; // 1.0, 0.5, 0.0 RGB
purpleColor; // 0.5, 0.0, 0.5 RGB
brownColor; // 0.6, 0.4, 0.2 RGB
clearColor; // 0.0 white, 0.0 alpha
```

让我们修改`initWithSize`方法，通过替换`SKBMyScene.m`文件中的原始代码为你自己的代码，使其匹配以下内容：

```objc
-(id)initWithSize:(CGSize)size {
    if (self = [super initWithSize:size]) {
        /* Setup your scene here */
        self.backgroundColor = [SKColor blackColor];
    }
    return self;
}
```

非常简单——你的`SKScene`现在将拥有一个纯黑色的背景。

### 启动画面


