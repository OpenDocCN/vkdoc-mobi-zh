# 第 4 章：快速构建输入驱动型游戏

**93**

我们通过调用`coinsGame`上的`findMatchingRows`和`findMatchinCols`来找到所有匹配的行和列。获取这些匹配数组后，我们希望将所有参与匹配的硬币动画化到屏幕外。如果存在`matchingRows`，我们为每个硬币创建一个新的`CABasicAnimation`，修改底层`CALayer`对象的`x`位置值。无需设置`x`的精确起始值和结束值，只需指定`byValue`，并将其设为`coinsView`的宽度。通过指定`byValue`，我们可以将此动画应用于行中所有硬币，因为该动画会自动推断`fromValue`为每个硬币视图的起始`x`值，而`toValue`将为`fromValue + byValue`。

创建完匹配行和列的动画后，我们检查本轮总共产生了多少匹配。如果此值大于零，则更新`coinsGame`对象的分数，并通知委托分数已更改。如果未找到匹配，我们检查游戏是否结束。如果游戏结束，我们使用`NSTimer`类在 1 秒后调用`doEndGame`。此延迟是出于美观考虑；在显示高分之前稍作停顿是很好的。如果游戏尚未结束，我们只需重新开始接受输入。**列表 4–28**展示了任务`doEndGame`，它只是通知委托游戏已结束。

**列表 4–28.** *CoinsController.m (doEndGame)*

```objectivec
-(void)doEndGame{

[delegate gameOver:self with: coinsGame];

}
```

之前在**列表 4–27**中创建的动画在完成时会调用任务`animationDidStop:finished:`，这与缩放动画的方式相同。我们使用此回调来随机化匹配，并调用`updateCoinViews`，如**列表 4–29**所示。

**列表 4–29.** *CoinsController.m (updateCoinViews)*

```objectivec
-(void)updateCoinViews{

int rowCount = [coinsGame rowCount];

int colCount = [coinsGame colCount];

for (NSNumber* row in matchingRows){

for (int c=0;c<colCount;c++){

Coord coord = CoordMake([row intValue], c);

[self spinCoinAt:coord];

}

}

for (NSNumber* col in matchingCols){

for (int r=0;r<rowCount;r++){

Coord coord = CoordMake(r, [col intValue]);

[self spinCoinAt:coord];

}

}

[self checkMatches];

}
```

任务`updateCoinViews`在代表硬币的`UIImageView`完全移出屏幕时被调用。此任务遍历之前匹配的每一组行和列，并在其原始位置再次旋转硬币，但使用与新值匹配的图像。最后，再次调用`checkMatches`，以处理因新随机值产生新匹配的任何情况。

[www.it-ebooks.info](http://www.it-ebooks.info/)

![](img/00354.png)

**94**

