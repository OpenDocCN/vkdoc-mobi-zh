# 第 3 章：探索游戏应用生命周期

**63**

## 实现生命周期任务

由于我们的应用支持后台执行，我们将归档逻辑放入 `applicationDidEnterBackground:` 任务中，如**清单 3–18**所示。

**清单 3–18.** *Sample_03AppDelegate.m (applicationDidEnterBackground:)*

```
- (void)applicationDidEnterBackground:(UIApplication *)application

{

NSString* gameArchivePath = [self gameArchivePath];

[NSKeyedArchiver archiveRootObject:[gameController currentGame] toFile: gameArchivePath];

}
```

为了归档我们的游戏状态，**清单 3–18** 显示我们再次使用 `NSKeyedArchiver` 类来归档 `CoinsGame` 对象，但这次我们将它归档到一个文件中。变量 `gameArchivePath` 是我们将用作归档的文件的路径。我们通过调用 `gameArchivePath` 任务来获取此路径，如**清单 3–19**所示。

**清单 3–19.** *Sample_03AppDelegate.m (gameArchivePath)*

```
-(NSString*)gameArchivePath{

NSArray* paths = NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES);

NSString* documentDirPath = [paths objectAtIndex:0];

return [documentDirPath stringByAppendingPathComponent:@"GameArchive"];

}
```

**清单 3–19**中的 `gameArchivePath` 任务显示，我们使用 `NSSearchPathForDirectoriesInDomains` 函数，并传递 `NSDocumentDirectory`，与 `NSUserDomainMask` 进行掩码运算。末尾的 `YES` 表示我们想要将用于指示用户主目录的波浪号（`~`）扩展为完整路径。iOS 设备上的每个应用都有一个根目录，文件可以从中读取和写入。通过获取路径数组 `NSArray` 中的第零项，我们可以访问该目录。我们只需通过向 `documentDirPath` 的 `stringByAppendingPathComponent` 任务传递一个 `NSString` 来指定要使用的文件名。

当我们需要解归档 `CoinsGame` 对象时，显然也需要归档文件的路径。**清单 3–20** 显示了我们解归档该对象的位置。

**清单 3–20.** *Sample_03AppDelegate.m(application: didFinishLaunchingWithOptions:)*

```
- (BOOL)application:(UIApplication *)application

didFinishLaunchingWithOptions:(NSDictionary *)launchOptions

{

NSString* gameArchivePath = [self gameArchivePath];
```




`CoinsGame* existingGame;`

```
@try {
    existingGame = [[NSKeyedUnarchiver unarchiveObjectWithFile:gameArchivePath] retain];
}
@catch (NSException *exception) {
    existingGame = nil;
}
```

`[gameController setPreviousGame:existingGame];`
`[existingGame release];`
`[self.window makeKeyAndVisible];`
`return YES;`

在清单 3-20 中，我们查看的是应用程序完全加载并准备就绪时调用的任务。您应该还记得上一章的最后两行：它们将 UIWindow 显示在屏幕上并返回一切正常。该任务的前面部分涉及解归档游戏状态。使用`gameArcivePath`，我们在`NSKeyedUnarchiver`类上调用了`unarchiveObjectFromFile`。如果存在，这将返回我们之前归档的`CoinsGame`对象。如果是应用程序首次运行，`existingGame`将是`nil`。此外，如果发生异常，我们也将其设置为`nil`，因为我们知道调用`gameController`的`setPreviousGame:`方法可以处理`nil`值。为了完成这个循环，让我们在清单 3-21 中看看`GameController`如何处理这个解归档的`CoinsGame`。

**清单 3-21.** *`GameController.m` (`setPreviousGame:`)**

```
-(void)setPreviousGame:(CoinsGame*)aCoinsGame{
    previousGame = [aCoinsGame retain];
    if (previousGame != nil && [previousGame remaingTurns] > 0){
        [continueButton setHidden:NO];
    } else {
        [continueButton setHidden:YES];
    }
}
```

在清单 3-21 中，我们看到传入的`CoinsGame`被保存为本地字段`previousGame`并进行了保留。如果`previousGame`不为`nil`并且还有剩余回合，我们显示按钮`continueButton`；否则隐藏它。一旦用户看到 Welcome 视图，他们就能继续游戏，从上次中断的地方开始。

## 总结

在本章中，我们探讨了一个完整游戏所需的支持元素。这些元素包括建立应用程序的流程以及管理构成该流程的视图。我们研究了一种使用委托模式协调游戏行为（例如分数变化或游戏结束）与应用程序其他部分的方法。我们还研究了两种持久化数据的方法：通过用户设置或在磁盘上存储对象。这两种技术都使用了通过`NSCoding`协议公开的相同归档和解归档技术。

## 第 4 章 快速构建输入驱动型游戏

所有游戏都由用户输入驱动，但根据游戏在用户操作之间的行为方式，游戏可以分为两种类型。一种是动作游戏，无论用户是否提供输入，屏幕上的事件都会展开。动作游戏将在第 5 章中探讨。在本章中，我们将研究等待用户做出选择的游戏。这类游戏包括益智游戏和策略游戏。在本章中，我们将其称为 *输入驱动型游戏*。

尽管输入驱动型游戏的酷炫程度肯定不如动作游戏，但市场上仍有许多成功的此类游戏。从扫雷到数独再到愤怒的小鸟，这类游戏吸引了大量用户，因此理解这类游戏是如何实现的非常重要。图 4-1 展示了一个输入驱动型游戏的典型生命周期。

**图 4-1.** *Coin Sorter——一个简单的输入驱动型游戏*

在图 4-1 中，在任何初始设置之后，应用程序等待用户采取某些操作。当用户执行操作时，游戏状态会更新，并创建动画来反映用户的操作。由用户操作创建的动画可以很简单，例如高亮显示选择项，也可以很复杂，例如整个物理模拟。动画完成后，应用程序测试是否已达到游戏结束条件。如果没有，则应用程序再次等待用户输入。

在本章中，您将探索如何创建遵循图 4-1 所述流程的游戏。您还将了解将内容显示到屏幕以及创建动画的细节。

### 探索如何将内容显示到屏幕

在上一章中，我们组装了视图来创建一个完整的应用程序。我们使用 Interface Builder 创建 UI 元素，并且大多只是用一个视图替换另一个视图，从而将用户从一个视图移动到另一个视图。在本章中，我们将更深入地研究`UIView`以及如何使用它以编程方式创建动态内容。其中包括如何将组件放置在屏幕上以及如何为其设置动画。

#### 理解 UIView

类`UIView`是 UIKit 的基础组件类。所有其他 UI 元素（例如按钮、开关和滚动视图等）都是`UIView`的子类。甚至`UIWindow`类也是`UIView`的子类，因此当我们在屏幕上操作内容时，很多工作都涉及与`UIView`类交互。

`UIView`类的工作方式与其他编程环境中的基础 UI 组件类非常相似。每个`UIView`定义一个屏幕区域，并拥有许多子视图，这些子视图相对于其父视图进行绘制。图 4-2 展示了`UIView`实例的示例布局。

**图 4-2.** *嵌套的 UIView 实例*

在图 4-2 中，您可以在后面看到示例应用程序的根`UIWindow`。当用户看到游戏视图时，`UIWindow`有一个子视图，即与`GameController`类关联的`view`属性。当用户单击 New Game 按钮时，`GameController`将 Portrait Play 视图作为其`view`的子视图放置。Portrait Play 视图有五个子视图，在 XIB 文件中定义。其中四个子视图是`UILabel`，用于显示和跟踪用户的分数和剩余回合。第五个`UIView`是 Portrait Game Holder 视图，它负责定义放置来自`CoinsController`的视图的区域。来自`CoinsController`的视图有多个子视图，这些子视图构成了游戏的交互部分。稍后将更详细地研究这些视图，但就目前而言，请理解这些视图以与其他视图完全相同的方式添加到场景中。

关于图 4-2 的另一点需要注意的是，并非所有视图都放置在其父视图的相同相对位置。例如，Portrait Play 视图放置在`GameController`视图的左上角，而 Portrait Game Holder 视图放置在其父视图 Portrait Play 的大约一半高度处。子视图的`frame`属性决定了其相对于父视图的位置。图 4-3 更详细地展示了这一点。

**图 4-3.** *UIView 的 frame*

如图 4-3 所示，`UIView`的`frame`不仅描述了子视图的位置，还描述了视图的大小。通常，`frame`被认为描述了一个子视图的区域。`frame`属性是一个`CGRect`类型的结构体，由另外两个结构体`origin`和`size`组成。字段`origin`是`CGPoint`类型，以点为单位描述子视图左上角的位置。字段`size`是`CGSize`类型，描述子视图的宽度和高度（以点为单位）。

#### 核心图形类型定义



