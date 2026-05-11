# 游戏应用生命周期

大多数游戏由多个视图组成——不仅仅是游戏主要动作发生的视图。这些视图通常包括欢迎视图、高分视图，以及可能的一些其他视图。本章附带的示例代码旨在概述如何管理这些辅助视图。图 3-1 展示了本游戏中使用的视图流程图。

**图 3-1.** *Coin Sorter 应用流程*

在图 3-1 中，每个矩形代表游戏中的一个视图。箭头表示这些视图之间的转换。从左侧开始，有一个标题为`Launch Image`的框。这个视图与其他视图不同，因为它只是一个`PNG`文件，在应用控制权传递给应用委托之前显示。有关配置此图像的详细信息，请参阅附录 A。当应用完全加载后，我们显示`Loading`视图。此视图在屏幕上停留几秒钟，同时应用预加载一些图像。一旦图像加载完成，我们向用户显示`Welcome`视图。

图 3-1 中的`Welcome`视图为玩家提供了多个选项。他们可以继续旧游戏、开始新游戏、查看高分或查看游戏信息。如果用户玩游戏，他们会被引导到`Play`视图，这是本应用的主要动作发生地。当游戏结束时，用户会看到`High Scores`视图。当用户查看完高分后，他们会返回到`Welcome`视图，从而可以再次从可用选项中选择。

图 3-1 中呈现的应用流程绝不是唯一的选择，但它展示了一种常见模式。一旦你理解每个视图的作用以及应用状态的管理方式，扩展或修改此示例应该很简单。

**注意：** 尽管使用 Xcode 的 Storyboard 功能来布置此类工作流可能很有吸引力，但支持多种屏幕方向和多种设备使得使用 Storyboard 不切实际。理想情况下，Storyboard 将在未来版本的 Xcode 中改进，并成为管理应用生命周期的唯一工具。

---

## 每个视图所扮演的角色

应用中的每个视图都有不同的用途。通过理解每个视图的作用，你将明白为什么这个示例要按照这种方式组织。让我们从图 3-2 开始，它展示了启动图像和`Loading`视图。

**图 3-2.** *启动图像和 Loading 视图*

在图 3-2 中，我们看到左侧的启动图像，当初始化过程的控制权传递给应用委托时，它会显示。应用显示右侧图 3-2 所示的`Loading`视图。在这种情况下，`Loading`视图有一个`UIImageView`，使用与启动图像相同的图像。这不是必需的，但这是为用户提供无缝视觉体验的好方法。在更复杂的应用中，我们可能希望在加载图像时显示进度条。对于这么简单的应用，我们实际上不需要加载屏幕，因为使用的图像数量和大小都很小。在我们的案例中，我们只需在`Loading`视图中添加一个带有文本“Loading...”的`UILabel`。一旦应用的初始资源加载完毕，就会呈现`Welcome`视图，如图 3-3 所示。

**图 3-3.** *显示“继续游戏”按钮的 Welcome 视图*

图 3-3 所示的`Welcome`视图向玩家显示四个按钮。第一个按钮允许玩家继续之前的游戏。`New Game`按钮开始新游戏。`High Scores`和`About`按钮允许玩家查看历史高分和关于屏幕。`Welcome`视图是玩家的主屏幕。

查看图 3-1，我们看到所有未来的操作最终都会将玩家带回到这个视图。如果玩家选择继续之前的游戏或开始新游戏，则会呈现`Play`视图，如图 3-4 所示。

图 3-4 显示了`Play`视图，游戏的实际玩法发生在这里。在屏幕顶部，显示剩余回合数和当前分数。

带有几何形状的方形区域是游戏进行的地方。每个三角形、正方形和圆形代表不同类型的硬币。游戏的目标是创建同类硬币的行和列。重新排列硬币的唯一方法是选择两个硬币交换位置。当创建一行或一列同类硬币时，玩家的分数会增加，新的硬币会出现以替换匹配列或行中的硬币。玩家在游戏结束前只能交换硬币十次，但通过精心规划，玩家可以在一次交换中创建多个匹配。当游戏结束时，会显示`High Score`视图，突出显示玩家可能取得的新高分。图 3-5 展示了`High Score`视图。

**图 3-4.** *Play 视图——进行中的游戏*

**图 3-5.** *High Score 视图*

在图 3-5 中，我们看到`High Score`视图，从顶部数第三个分数是上一场游戏的分数。此视图中显示的高分会在应用会话之间保存。我们将在本章稍后探讨高分数据如何保存。当用户完成查看过去和当前的成功记录后，他们可以选择`Done`按钮返回`Welcome`视图。从`Welcome`视图，玩家可以点击`About`按钮查看一些游戏信息，如图 3-6 所示。

**图 3-6.** *Welcome 视图*

图 3-6 中呈现的`Welcome`视图在很大程度上是一个占位符。你应该将“Lorem ipsem…”替换为你的游戏描述。

在第二章中，我们探讨了如何配置包含游戏中各种 UI 元素的`XIB`文件。对于这个游戏，我们遵循非常相似的模式。

## 理解项目结构

即使是一个简单的游戏，在 Xcode 项目中也可能有数量惊人的文件。探索这些项目有助于你理解应用的每个部分在哪里定义以及它们如何组合在一起。图 3-7 显示了 Xcode 中的项目浏览器，其中展示了此项目中使用的多个文件。

**图 3-7.** *示例项目 03 的项目浏览器*

图 3-7 中显示的项目遵循与第二章项目相同的模式。共享类位于`Sample 03`组下，而设备特定类存储在`iPhone`和`iPad`组下。类`Sample_03AppDelegate`包含应用的初始化代码，以及在玩家退出应用时保存用户状态的代码。`XIB`文件的连接方式也与第二章的`XIB`文件非常相似：以`MainWindow`开头的`XIB`文件各自包含一个适当子类的`GameController`。类`GameController`包含管理应用状态的所有逻辑，而每个子类的`XIB`文件包含适合每个设备的`UI`元素。



`CoinsController`类描述了游戏的有趣部分，`CoinGame`是一个模型类，描述哪种类型的硬币在哪里，以及分数和剩余回合数。通过归档`CoinGame`类的对象，我们可以在应用程序退出时保存当前游戏的状态。

`HighscoreController`类管理高分视图并显示`Highscores`类中的数据。`Highscores`类包含一个`Score`对象数组，其中每个`Score`对象代表一个分数以及获得该分数的日期。通过序列化`HighScores`的实例，我们可以在游戏会话之间保留用户的高分。

视图配置的核心内容定义在两个`GameController`XIB 文件中。让我们来看看这些文件。

## 为多视图配置应用程序

虽然此应用程序在 iPhone 和 iPad 上都能很好地运行，但我们只看一下 iPhone 版本的`GameController`XIB 文件，如图 3-8 所示。

**图 3–8.** `GameController_iPhone.xib`

[www.it-ebooks.info](http://www.it-ebooks.info/)

![](img/00308.png)

**第 3 章：探索游戏应用程序生命周期**

**45**

在图 3-8 中，我们在左侧看到了`GameController_iPhone.xib`文件的详细信息。在右侧，我们看到了在`GameController.h`文件中定义的`IBOutlet`和`IBAction`。

## 回顾 `GameController_iPhone.xib`

在左侧的“对象”部分下，我们看到了一些`UIView`。当游戏进行时，会显示`Landscape Play`视图和`Portrait Play`视图，这是此应用程序唯一支持两种方向的时刻。为简单起见，其余视图仅支持纵向方向。

当应用程序初始化时，我们知道用户看到的第一个视图是`Loading`视图。之后，用户会被导向到`Welcome`视图。在图 3-8 中，我们可以看到这两个视图都在 XIB 文件中定义。我们还可以看到`About`视图也在 XIB 文件中定义。对于实际的游戏和高分视图，我们在 XIB 中看到了相应的`UIViewController`。这两个视图使用`UIViewController`处理，因为它们各自贡献了相当多的功能，因此在它们自己的`UIViewController`类中定义其行为是有意义的。作为此设计决策的附带好处，我们可以在其他应用程序中重用高分`UIViewController`。

## 回顾 `GameController.h`

在图 3-8 的右侧，我们看到了`GameController`类中定义的连接。

我们还可以看到，列出的大多数对象都连接到了各种连接上。

以这种方式连接这些组件，使我们能够在代码中轻松引用这些不同的组件。清单 3-1 显示了`GameController.h`中这些连接的定义。

**清单 3–1.** `GameController.h`

```
#import <UIKit/UIKit.h>

#import "CoinsController.h"

#import "CoinsGame.h"

#import "HighscoreController.h"

@interface GameController : UIViewController <CoinsControllerDelegate>{

IBOutlet UIView *landscapePlayView;

IBOutlet UIView *landscapeGameHolderView;

IBOutlet UIView *portraitPlayView;

IBOutlet UIView *portraitGameHolderView;

IBOutlet UIView *loadingView;

IBOutlet UIView *welcomeView;

IBOutlet UIButton *continueButton;

IBOutlet UIView *aboutView;

IBOutlet CoinsController *coinsController;

IBOutletCollection(UILabel) NSArray *remainingTurnsLabels;

IBOutletCollection(UILabel) NSArray *scoreLabels;

IBOutlet HighscoreController *highscoreController;

[www.it-ebooks.info](http://www.it-ebooks.info/)

![](img/00070.png)

**46**

**第 3 章：探索游戏应用程序生命周期**

CoinsGame* previousGame;

BOOL isPlayingGame;

}

-(void)setPreviousGame:(CoinsGame*)aCoinsGame;

-(CoinsGame*)currentGame;

- (IBAction)continueGameClicked:(id)sender;

- (IBAction)newGameClicked:(id)sender;

- (IBAction)highScoresClicked:(id)sender;

- (IBAction)aboutClicked:(id)sender;

- (IBAction)aboutDoneClicked:(id)sender;

- (IBAction)highscoreDoneClicked:(id)sender;

-(void)loadImages;

-(void)showPlayView: (UIInterfaceOrientation)interfaceOrientation;

@end
```



