# 在 UFOs 中整合所有内容

根据应用内购买的复杂程度，将其集成到代码中可能非常简单或非常困难。在我们的 UFOs 应用中，有一个非常简单的产品，用户支付一次性费用即可解锁不同颜色的飞船。当用户购买该产品时，我们在用户默认设置中存储一个键来反映这一点。要在代码中解锁此购买，我们只需检查该键，然后执行所需步骤。为此，我们需要向项目添加一些新的美术资源。这些已包含在第 11 章的示例代码中（可从 Apress 网站获取）。

完成此操作后，我们需要修改 `viewDidLoad` 方法来更改飞船图像。以下代码片段展示了这些更改：

```
-(void)viewDidLoad
{
    purchasedUpgrade = [[NSUserDefaults standardUserDefaults]
        boolForKey:@"shipPlusAvailable"];
    CGRect playerFrame = CGRectMake(100, 70, 80, 34);
    myPlayerImageView = [[UIImageView alloc] initWithFrame: playerFrame];
    myPlayerImageView.animationDuration = 0.75;
    myPlayerImageView.animationRepeatCount = 99999;
    NSArray *imageArray;
    if (purchasedUpgrade)
    {
        imageArray = [NSArray arrayWithObjects:
            [UIImage imageNamed: @"Ship1.png"],
            [UIImage imageNamed: @"Ship2.png"], nil];
    }
    else
    {
        imageArray = [NSArray arrayWithObjects:
            [UIImage imageNamed: @"Saucer1.png"],
            [UIImage imageNamed: @"Saucer2.png"], nil];
    }
    myPlayerImageView.animationImages = imageArray;
    [myPlayerImageView startAnimating];
    [self.view addSubview: myPlayerImageView];
}
```

## 总结

在本章中，我们介绍了 StoreKit 和应用内购买。通过利用 StoreKit，你获得了许多新的应用变现方式，从可扩展内容到为用户提供的特殊升级。你现在应该能够自信地为自己的应用内商店添加各种产品。虽然 StoreKit 并非 Game Center 或 Game Kit 的直接组成部分，但你无疑会发现应用内商店是你 iOS 软件中一个宝贵的补充。

我们花了一些时间讨论 ngmoco:) 及其在免费增值模式上的实验和成功。你现在应该能够自信地使用 iTunes Connect 以及完全设置应用内购买产品所需的所有操作，以及显示该购买所需的代码。

我们了解了如何处理购买失败以及购买成功的路径。我们还探讨了一些高级主题，例如验证收据和同时进行多次购买。最后，我们看到了如何将整个体验集成到我们的 UFOs 演示应用中。



