# 第 8 章：掌控事件发生的时机

将 `performSelectorOnMainThread:withObject:waitUntilDone:` 的多次调用合并成一次 `dispatch_async()` 调用并非绝对必要，但依笔者之见，这种做法能让代码的可读性大大提高。

### 使用全局调度队列

我们已经见过了调度队列的一个例子：主队列。任何与用户界面相关的方法或需要在主线程上运行的其他任务，你都可以将其分派到这个队列。对于希望在后台运行的任务，你可以使用 `dispatch_get_global_queue()` 函数获取一个全局队列，该函数接受两个参数：队列优先级和选项。第二个参数是为 Apple 未来使用预留的，因此目前只需传入 `0` 即可。

第一个参数应该是以下四个常量之一，这里按优先级从高到低列出：

- `DISPATCH_QUEUE_PRIORITY_HIGH`
- `DISPATCH_QUEUE_PRIORITY_DEFAULT`
- `DISPATCH_QUEUE_PRIORITY_LOW`
- `DISPATCH_QUEUE_PRIORITY_BACKGROUND`（适用于 iOS 5 及更高版本）

获取到调度队列后，其使用方法与上一示例中的主队列完全相同。

让我们修改之前的 Twitter 示例，改用调度队列而不是 `NSOperationQueue`。打开 `LCTTimelineViewController.m`，并按如下方式修改 `tableView:cellForRowAtIndexPath:` 方法：

```
- (UITableViewCell *)tableView:(UITableView *)tableView
         cellForRowAtIndexPath:(NSIndexPath *)indexPath
{
    static NSString *CellIdentifier = @"Cell";
    UITableViewCell *cell = [tableView
                             dequeueReusableCellWithIdentifier:CellIdentifier];
    if (cell == nil) {
        cell = [[UITableViewCell alloc]
                initWithStyle:UITableViewCellStyleSubtitle
                reuseIdentifier:CellIdentifier];
    }

    // 配置单元格...
    NSDictionary *tweet = [_tweets objectAtIndex:[indexPath row]];
    [[cell textLabel] setText:[tweet objectForKey:@"text"]];
    [[cell detailTextLabel] setText:[[tweet objectForKey:@"user"]
                                      objectForKey:@"name"]];
    NSString *profileImageURI = [[tweet objectForKey:@"user"]
                                  objectForKey:@"profile_image_url"];
    NSURL *profileImageURL = [NSURL URLWithString:profileImageURI];
    NSURLRequest *profileImageURLRequest = [NSURLRequest
                                            requestWithURL:profileImageURL];

    [_profileImageQueue addOperationWithBlock:^{
        dispatch_queue_t dispatchQueue =
            dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);
        dispatch_async(dispatchQueue, ^{
            NSURLResponse *response = nil;
            NSError *error = nil;
            NSData *imageData = [NSURLConnection
                                 sendSynchronousRequest:profileImageURLRequest
                                 returningResponse:&response
                                 error:&error];
            UIImage *image = [UIImage imageWithData:imageData];

            [[cell imageView] performSelectorOnMainThread:@selector(setImage:)
                                               withObject:image
                                            waitUntilDone:NO];
            [cell performSelectorOnMainThread:@selector(setNeedsLayout)
                                  withObject:nil
                               waitUntilDone:NO];

            dispatch_async(dispatch_get_main_queue(), ^{
                [[cell imageView] setImage:image];
                [cell setNeedsLayout];
            });
        });
    }];

    return cell;
}
```

首先，我们用默认队列优先级获取一个全局调度队列。接着，使用 `dispatch_async()` 将一个代码块调度到这个队列上。在这个代码块内部，我们第二次使用 `dispatch_async()` 来将一个代码块调度到主队列上。像这样嵌套调用 `dispatch_async()` 的情况并不罕见，实际上这恰恰是 Grand Central Dispatch 的一大优势。

既然我们已经开始使用这些队列，你就可以移除之前创建的 `NSOperationQueue` 变量了：

```
@interface LCTTimelineViewController () {
    NSTimer *_reloadTimer;
    NSArray *_tweets;
    NSOperationQueue *_profileImageQueue;
}
```



- (void)reloadButtonPressed:(id)sender;

- (void)reloadTweets:(NSTimer *)reloadTimer;

- (void)tweetButtonPressed:(id)sender;

@end

同样地，你可以将其从 `initWithStyle:` 方法中移除：

```objc
- (id)initWithStyle:(UITableViewStyle)style
{
    self = [super initWithStyle:style];
    if (self) {
        [self setTitle:@"Timeline"];
        UIBarButtonItem *reloadButton =
            [[UIBarButtonItem alloc]
                initWithBarButtonSystemItem:UIBarButtonSystemItemRefresh
                                    target:self
                                    action:@selector(reloadButtonPressed:)];
        [[self navigationItem] setLeftBarButtonItem:reloadButton];
        UIBarButtonItem *tweetButton =
            [[UIBarButtonItem alloc]
                initWithBarButtonSystemItem:UIBarButtonSystemItemCompose
                                    target:self
                                    action:@selector(tweetButtonPressed:)];
        [[self navigationItem] setRightBarButtonItem:tweetButton];
        _profileImageQueue = [[NSOperationQueue alloc] init];
    }
    return self;
}
```

很好。现在，构建并运行你的代码，你会看到一切仍如预期般运行。如果这就是 Grand Central Dispatch 能为你做的一切，那它已经是一个很棒的库了。它能做的远不止这些，但在深入探讨它的能力之前，我们先聊聊它的结构。

[www.it-ebooks.info](http://www.it-ebooks.info/)

