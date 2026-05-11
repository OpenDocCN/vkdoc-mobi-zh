# 我们也知道，在找到匹配行后，`CoinsController` 需要随机化所有匹配项，为用户的下一个回合做好准备。这是通过任务 `randomizeRows:` 和 `randomizeCols:` 完成的。任务 `randomizeRows:` 如代码清单 4–19 所示。

**代码清单 4–19.** *`CoinsGame.m` (`randomizeRows:`)*

```
-(void)randomizeRows:(NSMutableArray*)matchingRows{

    for (NSNumber* row in matchingRows){

        for (int c=0;c<colCount;c++){

            int index = [self indexForCoord:CoordMake([row intValue], c)];

            int newCoin = arc4random()%3;

            [coins replaceObjectAtIndex:index withObject:[NSNumber
numberWithInt:newCoin]];

        }

    }

}
```

任务 `randomizeRows:` 接收一个填充了 `NSNumber` 对象的 `NSMutableArray`。每个 `NSNumber` 代表一行。通过遍历每一行，我们为该行中的每个硬币设置一个新的随机值。

`randomizeCols:` 的实现类似；我们只是对传入的列中的每个硬币进行随机化。

现在你已经了解了 `CoinsGame` 类，可以进一步查看 `CoinsController`，了解该类如何使用存储在 `CoinsGame` 中的数据来解释用户输入、管理代表硬币的视图，并根据游戏状态创建动画。

## 解释用户输入

当用户触摸其中一枚硬币时，我们想将其标记为已选中。回顾代码清单 4–8，我们知道创建了一个 `UITapGestureRecognizer` 并将其添加到 UIView `coinsView` 中。每当这个 `UITapGestureRecognizer` 检测到点击手势时，它就会调用任务 `tapGesture:`。让我们在代码清单 4–20 中看看 `tapGesture:` 的第一部分。

**代码清单 4–20.** *`CoinsController.m` (`tapGesture:`，部分)*

```
- (void)tapGesture:(UIGestureRecognizer *)gestureRecognizer{

    if ([coinsGame remaingTurns] > 0 && acceptingInput){

        UITapGestureRecognizer* tapRegognizer =

        (UITapGestureRecognizer*)gestureRecognizer;

        CGPoint location = [tapRegognizer locationInView:coinsView];

        Coord coinCoord = [self coordFromLocation:location];

        if (!isFirstCoinSelected){//第一枚硬币

            isFirstCoinSelected = true;

            firstSelectedCoin = coinCoord;

            [self stopCoinAt: firstSelectedCoin];

        } else {

            ///见代码清单 4–24

        }

    }

}
```

代码清单 4–20 将触发此事件的 `UIGestureRecognizer` 作为参数。

我们将使用该对象来确定用户点击的位置。此任务中的第一个 `if` 语句检查是否还有剩余回合并且当前正在接受输入。变量 `acceptingInput` 在动画期间被设置为 `false`，以防止用户在过渡期间选择硬币，这可能会引起用户的混淆。在将 `gestureRecognizer` 转换为名为 `tapRecognizer` 的 `UITapGestureRecognizer` 之后，我们通过调用 `locationInView:` 并传入 `coinsView` 来获取点击的位置。点击的位置以名为 `location` 的 `CGPoint` 形式返回。为了知道哪个硬币被点击了，我们通过调用 `coordFromLocation:` 将 `location` 转换为 `Coord`，如代码清单 4–21 所示。

**代码清单 4–21.** *`CoinsController.m` (`coordFromLocation:`)*

```
-(Coord)coordFromLocation:(CGPoint) location{

    CGRect coinsFrame = [coinsView frame];

    Coord result;

    result.col = location.x / coinsFrame.size.width * [coinsGame colCount]; result.row = location.y / coinsFrame.size.height * [coinsGame rowCount]; return result;

}
```

任务 `coordFromLocation:` 接收一个 `CGPoint` 并将其转换为一个 `Coord`，该 `Coord` 将告诉我们用户点击了哪个硬币。为了找到硬币，将位置的 x 值除以 `coinsView` 的宽度，然后乘以列数。行数的计算方式类似。

在代码清单 4–20 中，在我们确定了哪个硬币被点击并将结果存储为变量 `coinCoord` 之后，我们检查该硬币是否之前已被选中。如果没有，我们将 `isFirstCoinSelected` 设置为 `true`，记录选中的硬币，并调用 `stopCoinAt:`，如代码清单 4–22 所示。



### `Listing 4–22`. `CoinsController.m (stopCoinAt:)`

```
-(void)stopCoinAt:(Coord)coord{

UIImageView* coinView = [[coinsView subviews] objectAtIndex:[coinsGame indexForCoord:coord]];

NSNumber* coin = [coinsGame coinForCoord:coord];

UIImage* image = imageForCoin([coin intValue]);

[coinView stopAnimating];

[coinView setImage: image];

}
```

任务`stopCoinAt:`接收要停止旋转的硬币的坐标。首先找到表示该硬币的`UIImageView`，称为`coinView`。我们通过调用`stopAnimation:`来停止旋转动画，但我们不仅希望停止它，还希望显示硬币的正面。我们可以通过先调用`coinsGame`中的`coinForCoord`来确定硬币的类型，然后调用`imageForCoin`来返回我们想要使用的`UIImage`，从而找到正确的图像。我们只需在`coinView`上调用`setImage`并传入名为`image`的`UIImage`。函数`imageForCoin`根据传入的`int`值返回一个`UIImage`，如`Listing 4–23`所示。

### `Listing 4–23`. `CoinsController.m (imageForCoin(int))`

```
UIImage* imageForCoin(int coin){

if (coin == COIN_TRIANGLE){

return [UIImage imageNamed:@"coin_triangle0001"];

} else if (coin == COIN_SQUARE){

return [UIImage imageNamed:@"coin_square0001"];

} else if (coin == COIN_CIRCLE){

return [UIImage imageNamed:@"coin_circle0001"];

}

return nil;

}
```

在`Listing 4–24`中，我们看到当之前选中一个硬币时执行的代码。

### `Listing 4–24`. `CoinsController.m (tapGesture:, continued)`

```
if (CoordEqual(firstSelectedCoin, coinCoord)){//re selected the first one.

isFirstCoinSelected = false;

[self spinCoinAt:firstSelectedCoin];

} else {//selected another one, do switch.

acceptingInput = false;

[coinsGame setRemaingTurns: [coinsGame remaingTurns] - 1];

[delegate turnsRemainingDecreased:self with: [coinsGame remaingTurns]];

isFirstCoinSelected = false;

secondSelectedCoin = coinCoord;

[self stopCoinAt:secondSelectedCoin];

[self doSwitch:firstSelectedCoin With:secondSelectedCoin];

}
```

如果用户选择了同一个硬币，我们只需再次旋转该硬币并将`isFirstCoinSelected`设置为`false`。然而，如果用户选择了不同的硬币，就该进行交换了。我们首先将`acceptingInput`设置为`false`，这样用户就不会在动画执行时中断我们。我们还需要减少剩余回合数，并通知委托这一变化。我们将`isFirstCoinSelected`设置为`false`，因为下次调用此方法时，不应有任何硬币被选中。在将第二个硬币的坐标记录到变量`secondSelectedCoin`后，我们停止选中的硬币并调用`doSwitch:With:`，传入第一个和第二个选中的硬币。任务`doSwitch`将在下一节的`Listing 4–25`中展示，它负责创建硬币交换位置的动画。

这引出了我们关于如何在不使用之前描述的静态`UIView`任务的情况下创建动画的讨论。

## 使用 Core Animation 实现视图动画

之前我们研究了`UIView`类中用于创建动画的任务。这些任务提供了对 iOS 上动画实现方式的抽象视角。本节将更深入地探讨如何使用名为 Core Animation 的框架定义动画。Core Animation 是负责 iOS 设备上大多数动画的框架，也是静态`UIView`任务在幕后使用的框架。

理解 Core Animation 的最佳方式是继续我们的示例，看看如何创建硬币交换位置的动画。图 4–8 展示了硬币交换位置的动画。

**Figure 4–8.** *硬币交换位置*



第一列第二行的硬币（正方形）正在与第二列最后一行的硬币（三角形）进行交换。每个硬币会缩小到几乎看不见，然后切换硬币类型，再恢复至正常大小。这一过程在任务 `doSwitch:With:` 中实现，如代码清单 4–25 所示。

**代码清单 4–25**：`CoinsController.m`（`doSwitch:With:`）

```objc
-(void)doSwitch:(Coord)coordA With:(Coord)coordB {

    [coinsGame swap:coordA With:coordB];

    coinViewA = [[coinsView subviews] objectAtIndex:[coinsGame indexForCoord:coordA]];
    coinViewB = [[coinsView subviews] objectAtIndex:[coinsGame indexForCoord:coordB]];
    for (UIView* coinView in [NSArray arrayWithObjects:coinViewA, coinViewB, nil]){

        CABasicAnimation* animScaleDown = [CABasicAnimation
            animationWithKeyPath:@"transform.scale"];
        [animScaleDown setValue:@"animScaleDown" forKey:@"name"];
        animScaleDown.fromValue = [NSNumber numberWithFloat:1.0f];
        animScaleDown.toValue = [NSNumber numberWithFloat:0.0f];
        animScaleDown.duration = 1.0;
        animScaleDown.timingFunction = [CAMediaTimingFunction
            functionWithName:kCAMediaTimingFunctionEaseIn];

        CABasicAnimation* animScaleUp = [CABasicAnimation
            animationWithKeyPath:@"transform.scale"];
        [animScaleUp setValue:@"animScaleUp" forKey:@"name"];
        animScaleUp.fromValue = [NSNumber numberWithFloat:0.0f];
        animScaleUp.toValue = [NSNumber numberWithFloat:1.0f];
        animScaleUp.duration = 1.0;
        animScaleUp.beginTime = CACurrentMediaTime() + 1.0;
        animScaleUp.timingFunction = [CAMediaTimingFunction
            functionWithName:kCAMediaTimingFunctionEaseOut];

        if (coinViewA == coinView){
            [animScaleDown setDelegate:self];
            [animScaleUp setDelegate:self];
        }

        [coinView.layer addAnimation:animScaleDown forKey:@"animScaleDown"];
        [coinView.layer addAnimation:animScaleUp forKey:@"animScaleUp"];
    }
}
```

任务 `doSwitch:With:` 负责更新模型和创建动画。要更新模型，我们只需在 `coinsGame` 上调用 `swap:With:`。要创建动画，我们需要使用名为 `CABasicAnimation` 的类。一个 `CABasicAnimation` 对象描述了对 `CALayer` 的某个变化。`CALayer` 是一个代表 `UIView` 视觉内容的对象。

到目前为止，我们一直认为 `UIView` 提供屏幕上的内容，这依然正确，但 `UIView` 使用 Core Animation 层来实现其绘制方式。因此，每个 `UIView` 都有一个类型为 `CALayer` 的 `layer` 属性。你需要理解这一点，才能明白 `CABasicAnimation` 是如何工作的。查看代码清单 4–25，你会发现 `CABasicAnimation` 是通过指定一个路径来创建的。在我们的例子中，路径是 `transform.scale`。这是一个指向我们想动画的 `UIView` 所关联的 `CALayer` 对象的路径。

对于每个 `UIView`（`coinViewA` 和 `coinViewB`），我们都会创建两个 `CABasicAnimation` 对象。当我们创建 `CABasicAnimation` 对象 `animScaleDown` 时，我们指定此 `CABasicAnimation` 将操作属性 `transform` 上的 `scale` 值。

我们通过设置 `animScaleDown` 的 `fromValue` 来指定 `scale` 的起始值，并通过设置 `toValue` 来指定结束值。`duration` 值表示我们期望此动画持续多少秒。通过指定 `timingFunction`，我们可以控制此动画发生的速率。在这种情况下，我们指定了 `kCAMediaTimingFunctionEaseIn`，它指示 `CABasicAnimation` 先慢后快。

`CABasicAnimation` `animScaleUp` 与 `animScaleDown` 类似。最大的区别在于 `fromValue` 和 `toValue` 是相反的。我们还将其 `beginTime` 值设置为未来 1 秒钟。这里的思路是：我们希望 `animScaleDown` 动画运行 1 秒钟，使硬币缩小到消失；然后当 `animScaleDown` 完成时，我们希望 `animScaleUp` 动画运行，将硬币缩放回原大小。



诀窍在于，在这两个动画之间，交换两个硬币视图所使用的图片。如果我们处理的是 `coinViewA`，那么可以通过将 `self` 设置为动画 `animScaleDown` 的代理来实现这一点，因为我们只需要被通知一次。我们还需要知道所有这些动画何时结束，因此我们也将 `self` 设置为 `animScaleUp` 的代理。设置了代理的动画在完成时会调用任务 `animationDidStop:finished:`，如**列表 4-26** 所示。

在这里，我们可以看到任务 `animationDidStop:finished:`。请注意，有三个动画调用了这个任务。前两个 `if` 语句用于处理**列表 4-25** 中描述的动画。当 `animScaleDown` 动画完成后，我们交换 `UIImageViews` 所使用的 `UIImages`，以表示两个被选中的硬币。

当动画 `animScaleUp` 完成后，我们需要检查是否有匹配项。调用 `checkMatches` 即完成此操作。我们还需要让最近被选中的硬币重新开始旋转。`checkMatches` 的实现如**列表 4-27** 所示。

```
#pragma mark - 列表 4-26. CoinsController (animationDidStop:finished:)

(void)animationDidStop:(CAAnimation *)theAnimation finished:(BOOL)flag{

if ([[theAnimation valueForKey:@"name"] isEqual:@"animScaleDown"]){

UIImage* imageA = [coinViewA image];

[coinViewA setImage:[coinViewB image]];

[coinViewB setImage:imageA];

} else if ([[theAnimation valueForKey:@"name"] isEqual:@"animScaleUp"]){

[self checkMatches];

[self spinCoinAt:firstSelectedCoin];

[self spinCoinAt:secondSelectedCoin];

} else if ([[theAnimation valueForKey:@"name"] isEqual:@"animateOffScreen"]){

[coinsGame randomizeRows: matchingRows];

[coinsGame randomizeCols: matchingCols];

[self updateCoinViews];

}

}
```

```
#pragma mark - 列表 4-27. CoinsController.m (checkMatches)

(void)checkMatches{

matchingRows = [coinsGame findMatchingRows];

matchingCols = [coinsGame findMatchingCols];

int rowCount = [coinsGame rowCount];

int colCount = [coinsGame colCount];

BOOL isDelegateSet = NO;

if ([matchingRows count] > 0){

for (NSNumber* row in matchingRows){

for (int c=0;c<colCount;c++){

CABasicAnimation* animateOffScreen = [CABasicAnimation

animationWithKeyPath:@"position.x"];

[animateOffScreen setValue:@"animateOffScreen" forKey:@"name"]; animateOffScreen.byValue = [NSNumber

numberWithFloat:coinsView.frame.size.width];

animateOffScreen.duration = 2.0;

animateOffScreen.timingFunction = [CAMediaTimingFunction

functionWithName:kCAMediaTimingFunctionEaseIn];

Coord coord = CoordMake([row intValue], c);

int index = [coinsGame indexForCoord:coord];

UIImageView* coinView = [[coinsView subviews] objectAtIndex: index]; if (c == 0){

[animateOffScreen setDelegate:self];

isDelegateSet = YES;

}

[coinView.layer addAnimation:animateOffScreen

forKey:@"animateOffScreenX"];

}

}

}

if ([matchingCols count] > 0){

for (NSNumber* col in matchingCols){

for (int r=0;r<rowCount;r++){

CABasicAnimation* animateOffScreen = [CABasicAnimation

animationWithKeyPath:@"position.y"];

[animateOffScreen setValue:@"animateOffScreen" forKey:@"name"]; animateOffScreen.byValue = [NSNumber

numberWithFloat:coinsView.frame.size.height];

animateOffScreen.duration = 2.0;

animateOffScreen.timingFunction = [CAMediaTimingFunction

functionWithName:kCAMediaTimingFunctionEaseIn];

Coord coord = CoordMake(r, [col intValue]);

int index = [coinsGame indexForCoord:coord];

UIImageView* coinView = [[coinsView subviews] objectAtIndex: index]; if (!isDelegateSet && r == 0){

[animateOffScreen setDelegate:self];

}

[coinView.layer addAnimation:animateOffScreen

forKey:@"animateOffScreenY"];

}

}

}

int totalMatches = [matchingCols count] + [matchingRows count];

if (totalMatches > 0){

[coinsGame setScore:[coinsGame score] + totalMatches];

[delegate scoreIncreases:self with:[coinsGame score]];

} else {

if ([coinsGame remaingTurns] <= 0){

}
```



```objectivec
//延迟调用委托上的 gameOver，以便硬币的 UIImageView 显示正确的硬币。

[NSTimer scheduledTimerWithTimeInterval:1 target:self

selector:@selector(doEndGame) userInfo:nil repeats:FALSE];

} else {

//所有匹配动画已完成，且剩余回合数。

acceptingInput = YES;

}

}
```

[www.it-ebooks.info](http://www.it-ebooks.info/)

![](img/00314.png)

