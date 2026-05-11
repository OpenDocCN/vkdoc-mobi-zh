# Core Graphics 类型与函数

`Core Graphics`定义了许多类型、结构体和函数，包括之前提到的`CGRect`、`CGPoint`和`CGSize`。清单 4-1 展示了这三个结构体的定义。

**清单 4-1.** *CGGeometry.h（`CGRect`、`CGPoint`和`CGSize`）*

```
/* Points. */
struct CGPoint {
    CGFloat x;
    CGFloat y;
};
typedef struct CGPoint CGPoint;

/* Sizes. */
struct CGSize {
    CGFloat width;
    CGFloat height;
};
typedef struct CGSize CGSize;

/* Rectangles */
struct CGRect {
    CGPoint origin;
    CGSize size;
};
typedef struct CGRect CGRect;
```

清单 4-1 展示了定义了子视图相对于其父视图区域的每个核心结构体。这些结构体定义在文件`CGGeometry.h`中，该文件属于`Core Graphics`框架，是任何 iOS 项目的标准组成部分。注意，`x`、`y`、`width`和`height`值被定义为`CGFloat`。这些值的单位是点（points），而非像素（pixels）。

这种差异起初很微妙，但这里的关键在于，你用这些值指定的坐标和大小是分辨率无关的。关于点与像素的进一步讨论，请参见附录 A。

**提示：** 诸如`CGRect`等结构体名称中的字母`CG`指的是`Core Graphics`框架。因此，当需要精确表述时，`CGRect`被称为*Core Graphics Rect*。

要创建清单 4-1 中的任何基本几何类型，`CGGeometry.h`中定义了一个内联工具函数，如清单 4-2 所示。

**清单 4-2.** *CGGeometry.h（`CGPointMake`、`CGSizeMake`和`CGRectMake`）*

```
/*** Definitions of inline functions. ***/
CG_INLINE CGPoint
CGPointMake(CGFloat x, CGFloat y)
{
    CGPoint p; p.x = x; p.y = y; return p;
}

CG_INLINE CGSize
CGSizeMake(CGFloat width, CGFloat height)
{
    CGSize size; size.width = width; size.height = height; return size;
}

CG_INLINE CGRect
CGRectMake(CGFloat x, CGFloat y, CGFloat width, CGFloat height)
{
    CGRect rect;
    rect.origin.x = x; rect.origin.y = y;
    rect.size.width = width; rect.size.height = height;
    return rect;
}
```

清单 4-2 展示了可用于创建新几何数值的工具函数。这里仅显示了定义，因为这些函数的声明很简单。类型`CGFloat`被简单地定义为`float`。可以推测，这可能会被修改以支持不同的架构。此外，`CG_INLINE`被简单地定义为`static inline`，表示编译器可以将编译后的版本注入到调用代码中。

### 使用 Core Graphics 类型

`Core Graphics`定义的类型和函数相当直接。让我们看一个示例来说明它们的用法。清单 4-3 展示了一个`UIView`被添加到另一个`UIView`的特定区域。

**清单 4-3.** *添加子视图并指定 Frame 的示例*

```
UIView* parentView = [[UIView alloc] initWithFrame:CGRectMake(0, 0, 480, 320)];
UIView* subView = [UIView new];

CGRect frame = CGRectMake(200, 100, 30, 40);
[subView setFrame:frame];
[parentView addSubview:subView];
```

在清单 4-3 中，我们以两种不同的方式创建了两个`UIView`。`UIView`类型的`parentView`以两种方式中更标准的方式创建：调用`alloc`，然后调用`initWithFrame:`并传入一个新的`CGRect`来指定其位置和大小。创建`UIView`的另一种完全有效的方法是简单地调用`new`，然后设置`frame`属性，就像我们对`subView`所做的那样。这段代码中做的最后一件事是通过调用`addSubview:`将`subView`添加到`parentView`。结果将类似于前面展示的图 4-3。

现在你已经理解了`UIView`子类的基础知识以及它们如何嵌套和放置，可以看看一些基本的动画了。这将为你提供探索我们简单游戏所需的理解。

### 理解动画

创建事件驱动游戏有两个关键组成部分。第一个是创建动画，第二个是管理游戏状态。在 iOS 应用中有多种创建动画的方式。在前一章中，我们实现了视图间过渡的动画。在本节中，我们将回顾它是如何完成的，并展示一个类似的例子来加深你的理解。在本章末尾，你将看到第三种技术，它探索了前两个示例所使用的后备类。

#### UIView 的静态动画任务

类`UIView`具有静态任务，旨在简化基本动画的创建。这些同样的任务可用于触发预设动画，例如用于从一个视图切换到另一个视图的过渡。图 4-4 展示了当用户从高分视图切换到欢迎视图时发生的预设动画。

**图 4-4.** *翻转动画*

从图 4-4 的左上角开始，高分视图从左向右翻转，直到它变成一条细缝。动画继续进行，以平滑、视觉上吸引人的过渡方式展示欢迎视图。这只是 iOS 中许多内置动画之一。用户已经了解到，这种标准过渡表示上下文的变化。要理解这个动画是如何触发的，让我们先看看在第 3 章中执行从高分视图切换到欢迎视图的步骤。这些步骤如清单 4-4 所示。

**清单 4-4.** *GameController.m（`highscoreDoneClicked:`）*

```
- (IBAction)highscoreDoneClicked:(id)sender {
    [UIView beginAnimations:@"AnimationId" context:@"flipTransitionToBack"];
    [UIView setAnimationDuration:1.0];
    [UIView setAnimationTransition:UIViewAnimationTransitionFlipFromLeft forView:self.view cache:YES];
    [highscoreController.view removeFromSuperview];
    [self.view addSubview: welcomeView];
    [UIView commitAnimations];
}
```

清单 4-4 展示了当用户触摸高分视图中的“完成”按钮时调用的任务。该任务的第一行调用了静态任务`beginAnimations:context:`。这个任务有点像启动一个数据库事务，因为它正在创建一个动画对象，并且在调用`commitAnimations`之前对`UIView`实例发生的任何变化都是此动画的一部分。传递给`beginAnimations:context:`的第一个`NSString`是后备动画对象的标识符。第二个`NSString`与第一个类似，用于提供关于动画创建时间的额外上下文。在这个例子中，这两个字符串可以是`nil`，因为我们在定义动画之后实际上并不太关心它。在下一个例子中，我们将利用这些字符串来定义动画完成时要运行的代码。

在调用`beginAnimations:context:`之后，通过调用`setAnimationDuration:`定义动画的持续时间。传入的值是动画应运行的秒数。对`setAnimationTransition:forView:cache:`的调用表明我们希望使用 iOS 附带的预设动画之一。我们选择的动画是`UIViewAnimationFlipFromLeftForView`。由于这是一个过渡动画，我们指定视图`self.view`，即其内容正在发生变化的父视图。



动画设置完成后，下一步（如代码清单 4-4 所示）是对场景进行我们想要动画化的修改。由于此任务负责将显示视图从“高分”视图切换到“欢迎”视图，我们只需将视图 `highscoreController.view` 从其父视图（`self.view`）中移除，并将 `welcomeView` 添加到 `self.view` 中。最后，我们提交动画，使其向用户展示。

我们可以利用这些 `UIView` 的静态任务来创建其他动画，而不仅仅是 iOS 提供的那些。让我们更新从欢迎屏幕到高分屏幕的转场效果，使一个视图淡出到另一个视图，如图 4-5 所示。

**图 4-5.** *淡入淡出转场动画*

在此淡入淡出转场中，欢迎视图被替换为高分视图。这是通过在一段时间内改变每个视图的不透明度来实现的。在此动画期间，两个视图都是场景的一部分。最后，我们希望移除欢迎视图，以与我们管理游戏中视图的方式保持一致。创建此转场的代码如代码清单 4-5 所示。

**代码清单 4-5.** *GameController.m (highScoresClicked:)*

```
- (IBAction)highScoresClicked:(id)sender {
// 将高分视图设置为完全透明，并将其添加到欢迎视图之上。
[highscoreController.view setAlpha:0.0];
[self.view addSubview:highscoreController.view];
// 设置动画
[UIView beginAnimations:nil context:@"fadeInHighScoreView"];
[UIView setAnimationDuration:1.0];
[UIView setAnimationDelegate:self];
[UIView setAnimationDidStopSelector:@selector(animationDidStop:finished:context:)];
// 进行修改
[welcomeView setAlpha:0.0];
[highscoreController.view setAlpha:1.0];
[UIView commitAnimations];
}
```

当用户在首页视图中点击“高分榜”按钮时，会调用代码清单 4-5 中所示的任务。为了创建淡入淡出效果，我们利用了 `UIView` 类的静态动画任务，但首先需要设置一些内容。我们首先将与 `highscoreController` 关联的视图的 alpha 属性设置为 `0.0`，即完全透明。然后，我们将高分视图添加到根视图中。这会将其放置到已有的 `welcomeView` 之上，但由于它是透明的，我们只能看到欢迎屏幕。

通过调用 `beginAnimations:context:` 任务来设置动画，并使用 `setAnimationDuration:` 任务设置持续时间。下一步是为正在创建的动画设置代理。我们还希望调用 `setAnimationDidStopSelector:` 来设置动画完成时应调用的任务。动画设置完成后，我们只需将 `welcomeView` 的 alpha 属性设置为 `0.0`，并将高分视图的 alpha 设置为 `1.0`。这表明在动画结束时，我们希望欢迎视图变为透明，而高分视图变为完全不透明。然后，我们通过调用 `commitAnimations` 来指示已完成最终状态的设置。

我们将 `self` 设置为动画的代理，并设置任务 `animationDidStop:finished:context:` 在动画结束时被调用。这样做是为了在动画结束时进行一些清理工作，并确保应用程序处于正确状态。代码清单 4-6 展示了此回调方法的实现。

**代码清单 4-6.** *GameController.m (animationDidStop:finished:context:)*

```
- (void)animationDidStop:(NSString *)animationID finished:(NSNumber *)finished context:(void *)context{
if ([@"fadeInHighScoreView" isEqual:context]){
[welcomeView removeFromSuperview];
[welcomeView setAlpha:1.0];
}
}
```

代码清单 4-6 中的任务在淡入淡出动画结束时被调用。在此任务中，我们希望从场景中移除 `welcomeView` 并将其 alpha 属性重置为 `1.0`。我们将其移除，因为这是其他转场预期的状态。将 alpha 重置为 `1.0` 可确保当 `welcomeView` 重新添加回场景时，它是可见的。

你已经了解了如何使用 `frame` 属性将视图添加到父视图。你还通过探索淡入淡出转场，学会了如何创建影响 `UIView` 属性的动画。我们将结合这些概念，向你展示如何对游戏中的硬币进行动画处理，但首先你需要了解我们如何设置相关类，以便一切都能说得通。

## 构建游戏 Coin Sorter

到目前为止，我们已经探讨了将内容显示到屏幕上的部分细节。

在前面的章节中，我们探讨了控制器类的角色以及它们如何管理数据和视图之间的关系。创建 Coin Sorter 游戏的最后一步是结合这些概念。本节将详细介绍 `CoinController` 类的生命周期，并涉及我们已探讨过的基本概念。图 4-6 显示了由 `CoinController` 类控制的屏幕区域。

在这个 5x5 的硬币网格中，用户可以选择两枚硬币交换位置。目标是创建相同硬币的行或列以形成匹配组合。当创建匹配时，屏幕上的硬币会有动画效果，并且用户的分数会增加。用户有 10 次机会尽可能多地创建匹配。图 4-7 显示了游戏的生命周期。

**图 4-6.** *CoinController 的视图*

图 4-7 展示了单次游戏的应用程序流程。设置完成后，应用程序等待用户选择一枚硬币。如果未选择第一枚硬币，应用程序仅记录该硬币被选中并返回等待用户。当用户第二次选择硬币时，应用程序会检查刚选中的硬币是否与第一次选中的相同。如果是，则取消选中该硬币。这允许用户改变主意。如果用户选择了不同的硬币，则通过修改 `CoinGame` 对象来更新游戏状态，并创建动画，展示两枚硬币交换位置。当硬币交换动画完成后，应用程序检查是否存在任何匹配。如果存在，则动画化其移除过程，并用新硬币更新 `CoinGame` 以替换刚刚移除的硬币。由于添加新硬币可能会产生额外的匹配，应用程序会再次检查匹配。

这个过程理论上可能永远持续下去，但实际上并不会。最终，将没有需要移除的匹配。

当没有匹配时，应用程序检查用户是否还有剩余次数。如果有，则返回等待用户输入。如果没有，则游戏结束。

**图 4-7.** *Coin Sorter 生命周期*

## 实现游戏状态

图 4-7 中展示的逻辑在 `CoinsController` 类中实现。为了帮助你理解 `CoinsController` 类如何创建 Coin Sorter 游戏，我们来看一下头文件。你可以在其中了解该类的概述，如代码清单 4-7 所示。

**代码清单 4-7.** *CoinsController.h*

```
#import <UIKit/UIKit.h>
#import "CoinsGame.h"

@class CoinsController;

@protocol CoinsControllerDelegate <NSObject>
-(void)gameDidStart:(CoinsController*)aCoinsController with:(CoinsGame*)game;
-(void)scoreIncreases:(CoinsController*)aCoinsController with:(int)newScore;
-(void)turnsRemainingDecreased:(CoinsController*)aCoinsController
with:(int)turnsRemaining;
```



---
-(void)gameOver:(`CoinsController*`)aCoinsController with:(`CoinsGame*`)game;

@end

[www.it-ebooks.info](http://www.it-ebooks.info/)

![](img/00113.png)

**第 4 章：快速构建一个输入驱动的游戏**

**77**

```
@interface CoinsController : UIViewController {

CoinsGame* coinsGame;

UIView* coinsView;

NSMutableArray* imageSequences;

BOOL isFirstCoinSelected;

Coord firstSelectedCoin;

Coord secondSelectedCoin;

BOOL acceptingInput;

UIImageView* coinViewA;

UIImageView* coinViewB;

NSMutableArray* matchingRows;

NSMutableArray* matchingCols;

IBOutlet id<CoinsControllerDelegate> delegate;

}

@property (nonatomic, retain) CoinsGame* coinsGame;

@property (nonatomic, retain) id<CoinsControllerDelegate> delegate;

+(NSArray*)fillImageArray:(int)coin;

-(void)loadImages;

-(void)newGame;

-(void)continueGame:(CoinsGame*)aCoinsGame;

-(void)createAndLayoutImages;

-(void)tapGesture:(UIGestureRecognizer *)gestureRecognizer;

-(Coord)coordFromLocation:(CGPoint) location;

-(void)doSwitch:(Coord)coordA With:(Coord)coordB;

-(void)checkMatches;

-(void)updateCoinViews;

-(void)spinCoinAt:(Coord)coord;

-(void)stopCoinAt:(Coord)coord;

-(void)doEndGame;

@end
```

这个头文件定义了`CoinsController`类及其委托协议`CoinsControllerDelegate`（已在第 3 章讨论）。`CoinsGame`字段用于存储游戏状态，该对象可归档保存游戏状态，或传给`CoinsController`实例以恢复游戏。`UIView coinsView`是白色游戏区域。`BOOL isFirstCoinSelected`用于追踪用户是否已选择一枚硬币。两个`Coord`结构体记录当前选中的硬币。我们稍后将介绍`Coord`结构体。`BOOL acceptingInput`在动画播放时用于阻止用户输入。

`coinViewA`和`coinViewB`这两个`UIImageView`字段，以及`NSMutableArray`变量`matchingRows`和`matchingCols`，在动画过程中用于追踪动画对象。最后一个字段是该类的委托，必须实现`CoinsControllerDelegate`协议。

[www.it-ebooks.info](http://www.it-ebooks.info/)

![](img/00235.png)

**78**

**第 4 章：快速构建一个输入驱动的游戏**

**初始化与设置**

`CoinsController`对象的生命周期始于`viewDidLoad`方法被调用时。因为我们已经在 XIB 文件中包含了`CoinsController`的实例，所以该方法会被自动调用。

清单 4-8 展示了这个方法的实现。

**清单 4-8** `CoinsController.m (viewDidLoad)`

```
- (void)viewDidLoad

{

[super viewDidLoad];

[self.view setBackgroundColor:[UIColor clearColor]];

CGRect viewFrame = [self.view frame];

//border is 3.125%

float border = viewFrame.size.width*.03125;

float coinsViewWidth = viewFrame.size.width-border*2;

CGRect coinsFrame = CGRectMake(border, border, coinsViewWidth, coinsViewWidth); coinsView = [[UIView alloc] initWithFrame: coinsFrame];

[coinsView setBackgroundColor:[UIColor whiteColor]];

[coinsView setClipsToBounds:YES];

[self.view addSubview:coinsView];

UITapGestureRecognizer* tapRecognizer = [[UITapGestureRecognizer alloc]

initWithTarget:self action:@selector(tapGesture:)];

[tapRecognizer setNumberOfTapsRequired:1];

[tapRecognizer setNumberOfTouchesRequired:1];

[coinsView addGestureRecognizer:tapRecognizer];

}
```

在`viewDidLoad`方法中，我们需要处理几项小任务。首先，调用父类的`viewDidLoad`实现。这不是绝对必要，但是一种良好实践。如果将来我们想改变`CoinsController`的父类，可能会花费大量时间排查初始化问题。

接着，`viewDidLoad`方法需要设置一些`UIView`实例来实现我们想要的效果。参见图 4-6，可以发现白色硬币区域周围有一个半透明边框。这个半透明视图实际上就是容器视图，因此为了确保其可见，我们将该类的根视图设置为完全透明。



白色方形区域是`UIView`，名为`coinsView`，它是用于显示硬币的视图的父视图。我们根据父视图的`frame`计算`coinsView`的大小。在 iPhone 上，`coinsViewWidth`的值为 300 点；在 iPad 上，`coinsViewWidth`为 720 点。将`coinsView`的背景设置为白色后，我们将其`clipToBounds`属性设置为`true`。这将防止硬币在离屏动画过程中被绘制到白色区域之外。

[www.it-ebooks.info](http://www.it-ebooks.info/)

![](img/00342.png)

**第 4 章：快速构建一个输入驱动的游戏**

**79**

`viewDidLoad`中的最后一个任务是向`coinsView`注册一个手势识别器。在这种情况下，我们希望知道用户何时点击`coinsView`，因此我们使用了一个名为`tapRecognizer`的`UITapGestureRecognizer`。在初始化`tapRecognizer`时，我们将`self`设置为目标，并指定任务`tapGesture:`作为当`tapRecognizer`检测到点击手势时要调用的任务。通过设置所需的点击次数和所需的手指数量，我们确保能够捕捉到用户做出的正确手势。将`tapRecognizer`添加到`coinsView`即完成了手势识别器的设置。

关于 iOS 中手势如何工作的更多信息，请参见第 8 章。

**开始新游戏**

在`viewDidLoad`被调用后，`CoinsController`将用于开始一个新游戏或继续一个旧游戏。首先，我们来看`newGame`任务，如代码清单 4-9 所示。

**代码清单 4-9.** *CoinsController.m (newGame)*

```
-(void)newGame{

for (UIView* view in [coinsView subviews]){

[view removeFromSuperview];

}

[coinsGame release];

coinsGame = [[CoinsGame alloc] initRandomWithRows:5 Cols:5];

[self createAndLayoutImages];

[delegate gameDidStart:self with: coinsGame];

acceptingInput = YES;

}
```

当在欢迎屏幕上点击“新游戏”按钮时，会调用`newGame`任务。首先要做的是从`coinsView`中移除旧的子视图（如果用户刚刚启动应用程序，可能没有任何子视图需要移除）。`coinsGame`对象在被设置为一个预填充随机硬币值的新实例之前被释放。下一步是调用`createAndLayoutImages`，它将在屏幕上放置硬币。该任务同时被`continueGame:`和`newGame`调用，所以我们将在查看`continueGame:`之后再来看它。最后要做的是通知任何委托一个新游戏已开始，并将`acceptingInput`设置为`YES`。

**继续游戏**

如果用户在最后一次退出时游戏正在进行中，则该用户可能希望从上次中断的地方继续游戏。这是通过在欢迎视图中点击“继续”按钮来完成的。当该按钮被按下时，会调用`continueGame`任务，该任务与`newGame`非常相似。代码清单 4-10 展示了`continueGame:`。

[www.it-ebooks.info](http://www.it-ebooks.info/)

![](img/00376.png)

**80**

**第 4 章：快速构建一个输入驱动的游戏**

**代码清单 4-10.** *CoinsController.m (continueGame:)*

```
-(void)continueGame:(CoinsGame*)aCoinsGame{

for (UIView* view in [coinsView subviews]){

[view removeFromSuperview];

}

[coinsGame release];

coinsGame = aCoinsGame;

[self createAndLayoutImages];

[delegate gameDidStart:self with: coinsGame];

acceptingInput = YES;

}
```

`continueGame:`任务接收一个`CoinsGame`对象作为参数，名为`aCoinsGame`。在清除`coinsView`的旧子视图后，我们将`coinsGame`设置为传入的`aCoinsGame`对象。调用`createAndLayoutImages`以将硬币添加到场景中。我们调用委托任务`gameDidStart:coinsGame:`，以便委托有机会更新跟踪当前得分和剩余回合数的`UILabel`对象。最后，我们将`acceptingInput`设置为`YES`。

**初始化每个硬币的 UIView**

如前所述，`createAndLayoutImages`任务被`newGame`和`continueGame:`调用。该任务负责游戏的初始设置，主要是为游戏中的每个硬币创建一个`UIImageView`。该任务如代码清单 4-11 所示。



**列表 4–11.** `CoinsController.m (createAndLayoutImages)`

```
-(void)createAndLayoutImages{

int rowCount = [coinsGame rowCount];

int colCount = [coinsGame colCount];

CGRect coinsFrame = [coinsView frame];

float width = coinsFrame.size.width/colCount;

float height = coinsFrame.size.height/rowCount;

for (int r=0;r<rowCount;r++){

for (int c=0;c<colCount;c++){

UIImageView* imageView = [[UIImageView alloc] init];

CGRect frame = CGRectMake(c*width, r*height, width, height);

[imageView setFrame:frame];

[coinsView addSubview: imageView];

[self spinCoinAt:CoordMake(r, c)];

}

}

}
```

`列表 4–11` 展示了游戏中将要使用的行数和列数。在本例中，两者均为五。`coinsView` 的框架存储在变量 `coinsFrame` 中以便引用，并计算了每个硬币视图的宽度和高度。两个 `for` 循环逐个创建 `imageView`，设置其框架，并将其添加到 `coinsView` 中。在嵌套循环中，调用了 `spinCoinAt:`，该方法用于创建旋转硬币的效果。注意传递给 `spinCoinAt:` 的参数是函数 `CoordMake` 的返回值。我们将在查看 `spinCoinAt:`（如 `列表 4–12` 所示）之后，再来研究这个函数和 `CoinsGame` 类。

**列表 4–12.** `CoinsController.m (spinCoinAt:)`

```
-(void)spinCoinAt:(Coord)coord{

UIImageView* coinView = [[coinsView subviews] objectAtIndex:[coinsGame indexForCoord:coord]];

NSNumber* coin = [coinsGame coinForCoord:coord];

NSArray* images = [imageSequences objectAtIndex:[coin intValue]];

[coinView setAnimationImages: images];

NSTimeInterval interval = (random()%4)/10.0+.6;

[coinView setAnimationDuration: interval];

[coinView startAnimating];

}
```

你可以看到 `spinCoinAt:` 接受一个类型为 `Coord` 的参数。该类型在 `CoinsGame.h` 中定义为结构体，表示特定行和列上的一个硬币。在该方法的第一个代码行中，结构体 `Coord` 用于查找代表该特定硬币的 `UIView` 的索引。之所以可行，是因为每个硬币恰好对应一个 `UIImageView`（这是在 `列表 4–11` 中设置的）。找到正确的 `UIImageView` 后，我们从 `coinsGame` 对象中获取该硬币的值。`NSNumber coin` 代表这组特定坐标下硬币的类型——可能是三角形、正方形或圆形。

利用硬币的 `intValue`，我们从 `imageSequences` 中取出一个名为 `images` 的 `NSArray`。该 `NSArray images` 存储了构成旋转硬币动画的所有图像。通过调用 `UIImageView coinView` 上的 `setAnimationImages:`，我们指定该 `UIImageView` 要循环显示 `images` 中的每个 `UIImage`，以产生动画效果。将动画时长设置为一个随机值并调用 `startAnimating`，即可创建旋转效果。在设置的这一阶段，应用程序看起来会与之前展示的 `图 4–6` 非常相似。现在，我们准备开始接受用户输入。

在探讨用户输入之前，我们应更仔细地研究 `CoinsGame` 类，以理解我们是如何表示这 25 个硬币及其类型的。

**模型**

你现在已经看到了设置代码，它使用 `UIViews` 和 `UIImageViews` 在屏幕上创建了代表游戏场景的界面。你知道游戏由 25 个硬币组成，排列在五乘五的网格中。现在，我们来看看负责管理这些数据的类，因此让我们查看 `列表 4–13` 中所示的 `CoinsGame.h`。

**列表 4–13.** `CoinsGame.h`

```
#import <Foundation/Foundation.h>

#define COIN_TRIANGLE 0
#define COIN_SQUARE 1
#define COIN_CIRCLE 2

struct Coord {
    int row;
    int col;
};
typedef struct Coord Coord;

CG_INLINE Coord
CoordMake(int r, int c)
{
    Coord coord;
    coord.row = r;
    coord.col = c;
    return coord;
}

CG_INLINE BOOL
CoordEqual(Coord a, Coord b)
{
    return a.col == b.col && a.row == b.row;
}
```


```objectivec
@interface CoinsGame : NSObject <NSCoding>{

NSMutableArray* coins;

int remainingTurns;

int score;

int colCount;

int rowCount;

}

@property (nonatomic, retain) NSMutableArray* coins;

@property (nonatomic) int remainingTurns;

@property (nonatomic) int score;

@property (nonatomic) int colCount;

@property (nonatomic) int rowCount;

-(id)initRandomWithRows:(int)rows Cols:(int)cols;

-(NSNumber*)coinForCoord:(Coord)coord;

-(int)indexForCoord:(Coord)coord;

-(void)swap:(Coord)coordA With:(Coord)coordB;

-(NSMutableArray*)findMatchingRows;

-(NSMutableArray*)findMatchingCols;

-(void)randomizeRows:(NSMutableArray*)matchingRows;

-(void)randomizeCols:(NSMutableArray*)matchingCols;

@end
```

[www.it-ebooks.info](http://www.it-ebooks.info/)

![](img/00026.png)

**第 4 章：快速构建一个输入驱动的游戏** 83

`代码清单 4-13`展示了类`CoinsGame`的头文件。在类的顶部，我们定义了三个常量：`COIN_TRIANGLE`、`COIN_SQUARE`和`COIN_CIRCLE`。这些值代表了游戏中的三种硬币类型。我们还定义了一个名为`Coord`的结构体，用于存储行/列值对。`Coord`结构体在之前的`代码清单 4-12`中用于标识特定的硬币。此外，还有两个与`Coord`结构体配套的函数。第一个函数`CoordMake`用于根据对应的行和列值创建一个`Coord`。第二个函数`CoordEqual`用于判断两个`Coord`是否指向同一枚硬币。

在`代码清单 4-13`中，类`CoinsGame`的接口声明遵循了`NSCoding`协议，因此我们知道可以对此类的实例进行归档和解档。我们还看到定义了一个名为`coins`的`NSMutableArray`。`coins`对象用于存储代表每个坐标上硬币类型的`NSNumber`值。此外，还使用了`int`类型来跟踪剩余回合数、得分以及本游戏中使用的行数和列数。

类`CoinsGame`定义了各种任务，其中一些任务的含义可能已经一目了然。我们知道需要一种交换两枚硬币的方法，因此`swap:With:`任务的使用应该不难理解。还有两个任务`findMatchingRows`和`findMatchingCols`，用于判断是否存在匹配。任务`randomizeRows`和`randomizeCols`则用于在发现匹配后设置新的硬币值。请注意，`findMatchingRows`返回一个`NSMutableArray`，而`randomizeRows`接收一个`NSMutableArray`。这里的思路是，当发现匹配且动画全部完成后，我们可以使用表示匹配的同一个`NSMutableArray`来指示哪些硬币需要被随机化。

`代码清单 4-13`还有两个以`Coord`为参数的任务。第一个是`coinForCoord:`，它接收一个`Coord`并返回`NSNumber`，指示该坐标处的硬币类型。第二个任务是`indexForCoord`，用于将`Coord`转换为适合作为数组索引的`int`值。该任务用于在`coinForCoord`中查找硬币对应的`NSNumber`，同时也被`CoinsController`用来查找硬币对应的正确`UIImageView`。

让我们来看看其中一些任务的实现，因为它们将在不同环节被`CoinsController`调用。我们从`initRandomWithRow:Col:`开始，如`代码清单 4-14`所示。

**代码清单 4-14.** *CoinsGame.m (initRandomWithRows:Cols:)*

```objectivec
-(id)initRandomWithRows:(int)rows Cols:(int)cols{

self = [super init];

if (self != nil){

coins = [NSMutableArray new];

colCount = cols;

rowCount = rows;

int numberOfCoins = colCount*rowCount;

for (int i=0;i<numberOfCoins;i++){

int result = arc4random()%3;

[coins addObject:[NSNumber numberWithInt:result]];

[www.it-ebooks.info](http://www.it-ebooks.info/)

![](img/00062.png)

**84** **第 4 章：快速构建一个输入驱动的游戏**

}

//确保我们不会以任何匹配的行和列开始游戏。

NSMutableArray* matchingRows = [self findMatchingRows];

NSMutableArray* matchingCols = [self findMatchingCols];

while ([matchingCols count] > 0 || [matchingRows count] > 0){

[self randomizeRows: matchingRows];
```


`[self randomizeCols: matchingCols];`

`matchingRows = [self findMatchingRows];`

`matchingCols = [self findMatchingCols];`

`remaingTurns = 10;`

`score = 0;`

`return self;`

在清单 4–14 中，我们看到用于创建带有随机硬币的新`CoinsGame`对象的初始化器任务。首先创建一个新的`NSMutableArray`，并将其赋值给变量`coins`，然后用游戏中的硬币总数填充它。结果的值将是 0、1 或 2。在设置好第一组随机值之后，我们必须确保游戏开始时没有任何匹配。这通过首先调用`findMatchingRows`和`findMatchingCols`查找任何匹配，并检查它们是否包含内容来实现。如果包含，我们就随机化这些匹配，直到找不到更多匹配为止。最后，我们将剩余回合数设置为 10，并确保分数从 0 开始。继续探索`CoinsGame`类，让我们看看清单 4–15 中所示的`coinForCoord:`。

**清单 4–15.** *`CoinsGame.m`（`coinForCoord:`）*

```
-(NSNumber*)coinForCoord:(Coord)coord{
    int index = [self indexForCoord:coord];
    return [coins objectAtIndex:index];
}
```

任务`coinForCoord:`接受一个`Coord`，并返回该位置上的硬币类型。

这通过查找`NSMutableArray` `coins`中给定索引处的`NSNumber`对象来实现。

索引通过调用`indexForCoord:`来确定，如清单 4–16 所示。

**清单 4–16.** *`CoinsGame.m`（`indexForCoord:`）*

```
-(int)indexForCoord:(Coord)coord{
    return coord.row*colCount + coord.col;
}
```

任务`indexForCoord:`接受一个`Coord`结构体。这个简单的任务将游戏中的列数乘以`coord`的行值，再加上列值。

`CoinsGame.m`中还有几个任务值得探索，以帮助你理解`CoinsController`在其生命周期不同阶段的行为。让我们继续查看清单 4–17 中所示的`swap:With:`任务。

[www.it-ebooks.info](http://www.it-ebooks.info/)

![](img/00102.png)

**第 4 章：快速构建输入驱动型游戏**

**85**

**清单 4–17.** *`CoinsGame.m`（`swap:With:`）*

```
-(void)swap:(Coord)coordA With:(Coord)coordB{
    int indexA = [self indexForCoord:coordA];
    int indexB = [self indexForCoord:coordB];
    NSNumber* coinA = [coins objectAtIndex:indexA];
    NSNumber* coinB = [coins objectAtIndex:indexB];
    [coins replaceObjectAtIndex:indexA withObject:coinB];
    [coins replaceObjectAtIndex:indexB withObject:coinA];
}
```

任务`swap:With:`接受两个`Coord`，`coordA`和`coordB`。该任务通过找到每个坐标的索引、该索引在`coins`中的当前值，并交换它们来切换这两个坐标上的硬币类型。我们知道，在硬币被交换后，`CoinsController`将需要查找任何匹配。这通过任务`findMatchingRows`和`findMatchingCols`完成。清单 4–18 展示了`findMatchingRows`。

**清单 4–18.** *`CoinsGame.m`（`findMatchingRows`）*

```
-(NSMutableArray*)findMatchingRows{
    NSMutableArray* matchingRows = [NSMutableArray new];
    for (int r=0;r<rowCount;r++){
        NSNumber* coin0 = [self coinForCoord:CoordMake(r, 0)];
        BOOL mismatch = false;
        for (int c=1;c<colCount;c++){
            NSNumber* coinN = [self coinForCoord:CoordMake(r,c)];
            if (![coin0 isEqual:coinN]){
                mismatch = true;
                break;
            }
        }
        if (!mismatch){
            [matchingRows addObject:[NSNumber numberWithInt:r]];
        }
    }
    return matchingRows;
}
```

任务`findMatchingRows`创建一个新的`NSMutableArray`，名为`matchingRows`，用于存储任何结果。通过依次检查每一行并观察每个列上的硬币来找到匹配的行。具体做法是将第 0 列的硬币存储为变量`coin0`，然后将`coin0`与其他列中的每个硬币进行比较。如果发现某个硬币与`coin0`不匹配，我们就知道该行没有匹配，并将`mismatch`设置为 true。如果没有发现不匹配，则将值`r`添加到`NSMutableArray` `matchingRows`，该数组即为本任务的返回值。`findMatchingCols`的实现略有不同，为简洁起见在此省略。



