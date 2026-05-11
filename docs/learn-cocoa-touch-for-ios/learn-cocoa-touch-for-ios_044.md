# 第 8 章：管理事件发生时的情况

**示例：在 TwitterExample 中安排选择器**

Twitter 应用的一个通用特性是，如果你让它们保持打开状态，它们会自动刷新数据。让我们使用选择器为 TwitterExample 应用添加这个功能。在 Xcode 中打开 TwitterExample 项目，并导航到时序视图控制器的实现文件（`LCTTimelineViewController.m`）。

为方法`reloadTweets`和`scheduleTweetRefresh`添加方法声明，添加到类扩展中，如下面粗体所示：

```
@interface LCTTimelineViewController () {
    NSArray *_tweets;
}
- (void)reloadButtonPressed:(id)sender;
- (void)reloadTweets;
- (void)scheduleTweetRefresh;
- (void)tweetButtonPressed:(id)sender;
@end
```

将这些方法的实现代码添加到此文件的实现部分，位于`reloadButtonPressed:`和`tweetButtonPressed:`的实现之间，如下所示：

```
- (void)reloadTweets
{
    LCTTwitterController *twitterController = [LCTTwitterController sharedInstance];
    [twitterController getTweetsInUserTimelineWithCompletionHandler:^(NSArray *tweets) {
        _tweets = tweets;
        [[self tableView] performSelectorOnMainThread:@selector(reloadData)
                                          withObject:nil
                                       waitUntilDone:NO];
        [self performSelectorOnMainThread:@selector(scheduleTweetRefresh)
                              withObject:nil
                           waitUntilDone:NO];
    }];
}

- (void)scheduleTweetRefresh
{
    [self performSelector:@selector(reloadTweets)
              withObject:nil
              afterDelay:15.0];
}
```

[www.it-ebooks.info](http://www.it-ebooks.info/)

