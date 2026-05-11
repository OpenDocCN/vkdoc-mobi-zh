# 第 5 章：快速构建逐帧游戏

### 列表 5–1. `Example01Controller.h`

```objc
#import <UIKit/UIKit.h>
#import <QuartzCore/CADisplayLink.h>
#import "Viper01.h"

@interface Example01Controller : UIViewController {
    CADisplayLink* displayLink;
    Viper01* viper;
}

-(void)updateScene;
-(void)viewTapped:(UIGestureRecognizer*)aGestureRecognizer;
@end
```

在列表 5–1 中，我们看到该类继承自`UIViewController`，并包含两个字段。第一个字段名为`displayLink`，类型为`CADisplayLink`，该类型从`QuartzCore`框架中导入。第二个字段名为`viper`，类型为`Viper01`，用于表示飞船。此外，还定义了两个任务：第一个是`updateScene`，用于更新构成场景的`UIViews`；第二个是`viewTapped`，用于管理用户输入。让我们看看列表 5–2 中的`Viper01`头文件，了解飞船是如何表示的。

### 列表 5–2. `Viper01.h`

```objc
#import <Foundation/Foundation.h>

@interface Viper01 : UIImageView {
}

@property float speed;
@property CGPoint moveToPoint;

-(void)updateLocation;
@end
```

在列表 5–2 中，我们看到`Viper01`继承自`UIImageView`，并包含两个属性。`speed`属性描述了毒蛇的移动速度，`moveToPoint`属性则记录了飞船在屏幕上的目标移动点。任务`updateLocation`负责逐步更新飞船的位置。

### 实现类

我们已经了解了本示例中将要使用的类的定义，现在来看看具体实现，首先从列表 5–3 中`Example01Controller`类的`viewDidLoad`任务开始。

### 列表 5–3. `Example01Controller.m` (viewDidLoad)

```objc
- (void)viewDidLoad
{
    [super viewDidLoad];
    [self setTitle:@"简单移动"];
    UITapGestureRecognizer* tapRecognizer = [[UITapGestureRecognizer alloc]
        initWithTarget:self action:@selector(viewTapped:)];
    [self.view addGestureRecognizer:tapRecognizer];

    viper = [Viper01 new];
    CGRect frame = [self.view frame];
    viper.center = CGPointMake(frame.size.width/2.0, frame.size.height/2.0);
    [self.view addSubview:viper];
    [viper setMoveToPoint:viper.center];

    displayLink = [CADisplayLink displayLinkWithTarget:self
                                            selector:@selector(updateScene)];
    [displayLink addToRunLoop:[NSRunLoop currentRunLoop]
                      forMode:NSDefaultRunLoopMode];
}
```

在列表 5–3 中，设置标题后，我们创建了一个`UITapGestureRecognizer`，并将其添加到与该`UIViewController`关联的根`UIView`上。将`UITapGestureRecognizer`添加到`UIView`中，当用户点击屏幕时，会调用`viewTapped:`任务，从而让我们有机会更新飞船的移动目标位置。

下一步是创建`Viper01`的新实例，并将其放置在根`UIView`的中心。我们还将`viper`的`moveToPoint`属性设置为屏幕中心，这样毒蛇一开始就保持静止。

最后要做的是指定一个需要周期性调用的任务，以便更新飞船的位置并创建动画。我们通过创建一个`CADisplayLink`并指定`updateScene`任务来实现这一点。一旦调用`addToRunLoop`，每次屏幕刷新时都会调用`updateScene`。下一节我们将研究`CADisplayLink`和`NSRunLoop`类，了解它们的具体作用。接下来，我们看看列表 5–4 中的`updateScene`。

### 列表 5–4. `Example01Controller.m` (updateScene)

```objc
-(void)updateScene{
    [viper updateLocation];
}
```

在列表 5–4 中，任务`updateScene`只是简单地调用了对象`viper`的`updateLocation`方法。在后续的示例中，我们会在`updateScene`任务中做更多操作，但这里我们只关注如何实现单艘飞船的移动。

### 移动飞船

每次调用`updateScene`时，都需要更新飞船的位置。这需要通过一些几何计算来确定飞船在动画每一帧的新位置。我们来看看`updateLocation`，了解该任务如何移动飞船。见列表 5–5。

### 列表 5–5. `Viper01.m` (updateLocation)

```objc
-(void)updateLocation{
    CGPoint c = [self center];
    float dx = (moveToPoint.x - c.x);
    float dy = (moveToPoint.y - c.y);
    float theta = atan(dy/dx);
    float dxf = cos(theta) * self.speed;
    float dyf = sin(theta) * self.speed;
    if (dx < 0){
        dxf *= -1;
        dyf *= -1;
    }
    c.x += dxf;
    c.y += dyf;
    if (abs(moveToPoint.x - c.x) < speed && abs(moveToPoint.y - c.y) < speed){
        c.x = moveToPoint.x;
        c.y = moveToPoint.y;
    }
    [self setCenter:c];
}
```

在列表 5–5 中，我们看到`Viper01`类的`updateLocation`任务。该任务负责将飞船向`moveToPoint`方向移动一帧的距离。由于类`Viper01`继承自`UIImageView`，我们只需设置用于指定其相对于父视图位置的标准属性即可移动它。我们可以使用`frame`属性，但还有一个名为`center`的属性，它是操作`UIView`框架的简写方式。

我们首先获取飞船当前中心点的副本，并将其存储在变量`c`中，然后开始计算新的中心位置。接下来，我们计算当前位置与目标位置在 X 轴和 Y 轴上的差值，分别存储在`dx`和`dy`中。查阅高中几何教材后，我们通过将`dy`除以`dx`并将结果传递给`atan`（反正切）函数，计算出描述移动方向的角度，结果存储在`theta`中。

我们希望飞船以`speed`个点的速度向`theta`方向移动。为了计算 X 和 Y 方向上的具体位移，需要进行一些几何运算。要计算 X 方向上的位移，我们取`theta`的余弦值乘以`speed`，结果存储在`dxf`中。类似地，要计算本帧在 Y 方向上的位移，我们取`theta`的正弦值乘以`speed`，结果存储在`dyf`中。为了最终确定这些值，我们检查`dx`是否为负值；如果是，则反转`dxf`和`dyf`的符号。

计算出`dxf`和`dyf`后，将它们分别加到变量`c`的`x`和`y`分量上。最后要做的是用`c`中的新值设置`center`属性。然而，如果飞船距离`moveToPoint`不到`speed`个点，就会超过目标点。我们不希望超过目标点，因为下一帧它会向相反方向再次超调，导致飞船在屏幕上抖动。为了解决这个问题，当飞船接近目标点时，我们直接将 X 和 Y 值设置为与`moveToPoint`的 X 和 Y 值相同。图 5–5 展示了所涉及的几何关系。

### 图 5–5. `updateLocation`任务涉及的几何关系

在图 5–5 中，我们看到 X 轴和 Y 轴，原点位于左上角，Y 轴正方向向下。飞船的框架显示为灰色矩形，起始中心点位于其中间。虚线三角形说明了如何根据`dx`、`dy`以及目标点的位置计算`theta`。黑色箭头的长度为`speed`，显示了一帧后中心点应处的位置。点状三角形展示了如何根据角度计算`dxf`和`dyf`。

### 响应用户点击



