# 第 8 章：管理事件发生时的情况

`scheduleTweetRefresh`方法会导致`reloadTweets`方法在被调用后 15 秒运行。当`reloadTweets`方法完成时，它自己会调用`scheduleTweetRefresh`，从而导致这个循环每大约 15 秒执行一次，具体取决于从 Twitter 获取推文所需的时间。为了让它在初始加载推文后运行，请在`viewWillAppear:`方法中添加下面粗体所示的行：

```
- (void)viewWillAppear:(BOOL)animated
{
    [super viewWillAppear:animated];
    LCTTwitterController *twitterController = [LCTTwitterController sharedInstance];
    NSString *title = [self title];
    [self setTitle:@"Authorizing…"];
    [twitterController authorizeAccountWithCompletionHandler:^{
        [self performSelectorOnMainThread:@selector(setTitle:)
                              withObject:@"Loading Tweets…"
                           waitUntilDone:NO];
        [twitterController getTweetsInUserTimelineWithCompletionHandler:^(NSArray *tweets) {
            [self performSelectorOnMainThread:@selector(setTitle:)
                                  withObject:title
                               waitUntilDone:NO];
            _tweets = tweets;
            [[self tableView] performSelectorOnMainThread:@selector(reloadData)
                                              withObject:nil
                                           waitUntilDone:NO];
            [self performSelectorOnMainThread:@selector(scheduleTweetRefresh)
                                  withObject:nil
                               waitUntilDone:NO];
        }];
    }];
}
```

现在，如果用户离开此屏幕，我们需要取消自动刷新。在`viewWillAppear:`方法之后、`viewDidUnload`方法之前，添加`viewWillDisappear:`方法的实现：

```
- (void)viewWillDisappear:(BOOL)animated
{
    [super viewWillDisappear:animated];
    [NSObject cancelPreviousPerformRequestsWithTarget:self
                                            selector:@selector(reloadTweets)
                                              object:nil];
}
```

[www.it-ebooks.info](http://www.it-ebooks.info/)

