# 第 2 章：设置你的游戏项目

**35**

## 响应屏幕方向变化

最后要做的是添加一些逻辑，根据设备的方向更改显示的 `UIView`。`UIViewController` 类指定了一个在设备旋转时调用的任务。该任务名为 `shouldAutorotateToInterfaceOrientation:`，它会收到一个指示设备新方向的常量。通过向此任务添加逻辑，我们可以实现所需的行为类型。

当创建一个新的 `UIViewController` 时，该任务的实现会包含在实现文件中。我们创建 `ViewController` 类的子类并连接 XIB 文件所做的工作，使我们能够使用 `shouldAutorotateToInterfaceOrientation:` 任务的单一实现。因此，打开 `ViewController_iPad.m` 和 `ViewController_iPhone.m`，并删除 `shouldAutorotateToInterfaceOrientation:` 任务的实现。这将确保在两个设备上，我们都将使用 `ViewController` 类中的实现。代码清单 2-7 展示了为我们提供所需行为的实现。

**代码清单 2–7.** *GameController.m (shouldAutorotateToInterfaceOrientation:)*

```objc
- (BOOL)shouldAutorotateToInterfaceOrientation:(UIInterfaceOrientation)interfaceOrientatio
{
    if (interfaceOrientation == UIInterfaceOrientationLandscapeLeft ||
        interfaceOrientation == UIInterfaceOrientationLandscapeRight){

        [portraitView removeFromSuperview];
        [self.view addSubview:landscapeView];
        [rockPaperScissorsController setup:landscapeHolderView.frame.size];
        [landscapeHolderView addSubview:rockPaperScissorsController.view];

    } else {

        [landscapeView removeFromSuperview];
        [self.view addSubview:portraitView];
        [rockPaperScissorsController setup:portraitHolderView.frame.size];
        [portraitHolderView addSubview:rockPaperScissorsController.view];
    }

    // 返回 YES 表示支持的方向
    return YES;
}
```

在代码清单 2-7 中，我们看到此任务接收了变量 `interfaceOrientation`，指示设备的新方向。最后，我们返回 `YES`，因为所有方向都支持。如果你正在创建不支持所有方向的游戏，则对不希望支持的方向返回 `NO`。在返回值之前，我们测试 `interfaceOrientation` 以查看当前是横屏还是竖屏方向。

由于设备实际上有四种方向，我们必须将 `interfaceOrientation` 与横屏右和横屏左进行比较。右/左指示符描述的是 Home 按钮的位置。

[www.it-ebooks.info](http://www.it-ebooks.info/)

![](img/00350.png)

