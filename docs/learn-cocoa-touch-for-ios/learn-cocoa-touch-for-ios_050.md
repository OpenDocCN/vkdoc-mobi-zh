# 第 8 章：管理事件发生时机

`[[UIBarButtonItem alloc] initWithBarButtonSystemItem:UIBarButtonSystemItemCompose target:self action:@selector(tweetButtonPressed:)];`

`[[self navigationItem] setRightBarButtonItem:tweetButton];`

`_profileImageQueue = dispatch_queue_create("com.learncocoatouch.profileImageQueue", DISPATCH_QUEUE_CONCURRENT);`

`_profileImageSemaphore = dispatch_semaphore_create(3);`

`return self;`

尽管我们使用了 ARC，但在完成操作后仍需释放这些对象。在`initWithStyle:`方法之后添加一个`dealloc`方法：

```
- (void)dealloc
{
    dispatch_release(_profileImageQueue);
    dispatch_release(_profileImageSemaphore);
}
```

接下来，让我们使用队列和信号量来控制并发连接的数量。按如下方式修改`tableView:cellForRowAtIndexPath:`：

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
```

`NSString *profileImageURI = [[tweet objectForKey:@"user"] objectForKey:@"profile_image_url"];`

`NSURL *profileImageURL = [NSURL URLWithString:profileImageURI];`

`NSURLRequest *profileImageURLRequest = [NSURLRequest requestWithURL:profileImageURL];`

`dispatch_queue_t dispatchQueue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0ul);`

```
dispatch_async(dispatchQueue, ^{
    dispatch_async(_profileImageQueue, ^{
        NSURLResponse *response = nil;
        NSError *error = nil;
        dispatch_semaphore_wait(_profileImageSemaphore,
                                DISPATCH_TIME_FOREVER);
        NSData *imageData = [NSURLConnection
                  sendSynchronousRequest:profileImageURLRequest
                  returningResponse:&response
                  error:&error];
        dispatch_semaphore_signal(_profileImageSemaphore);
        UIImage *image = [UIImage imageWithData:imageData];
        dispatch_async(dispatch_get_main_queue(), ^{
            [[cell imageView] setImage:image];
            [cell setNeedsLayout];
        });
    });
    return cell;
}
```

如你所见，我们将 URL 连接方法封装在信号量调用中。一旦从服务器获取到数据，就可以启动新的连接。构建并再次运行应用，你应该会看到图片加载出来。根据你的网络连接速度，你可能会注意到性能有所提升。

**分派时间**

你可能已经注意到前一个代码示例中的`DISPATCH_TIME_FOREVER`宏。`dispatch_semaphore_wait()`中的该参数指定了在继续执行之前等待信号量的最长时间。传入`DISPATCH_TIME_FOREVER`会使函数无限期等待。如果信号量未超时，`dispatch_semaphore_wait()`的返回值为零；如果超时发生，则返回非零值。要指定最大超时时间，你需要创建一个`dispatch_time_t`值。`dispatch_time_t`不是对象，因此不要使用以"create"结尾的函数。相反，使用`dispatch_time()`函数返回一个新的时间值。`dispatch_time()`接受两个参数，第一个参数是另一个`dispatch_time_t`；你创建的时间将相对于第一个参数。使用`DISPATCH_TIME_NOW`可以创建一个在不久的将来的时间。

第二个参数是添加到第一个参数以获取所需时间的纳秒数。你可以使用`NSEC_PER_SEC`宏来方便地计算要使用的纳秒数。要指定从现在起 15 秒后的分派时间，你可以按如下方式调用该函数：

`dispatch_time_t time = dispatch_time(DISPATCH_TIME_NOW, 15 * NSEC_PER_SEC);`

要将此时间用作信号量的超时时间，你可以按如下方式调用`dispatch_semaphore_wait()`：

```
long success = dispatch_semaphore_wait(mySemaphore, time);
if (success == 0) {
    // 超时未发生。
}
else {
    // 超时发生。
}
```

结合这些超时时间使用分派信号量，可以灵活控制应用中的资源分配。另一个使用`dispatch_time_t`类型的有用函数是`dispatch_after()`（以及它的对应函数`dispatch_after_f()`，它接受一个函数而不是代码块），该函数用于调度一段代码在稍后执行。使用`dispatch_after()`就像使用`dispatch_async()`一样，只是在其他参数之前多了一个参数，即一个`dispatch_time_t`值，用于指定何时调用该代码。如果你想调度一个代码块在 30 秒后运行，可以按如下方式操作：

```
dispatch_time_t time = dispatch_time(DISPATCH_TIME_NOW, 30 * NSEC_PER_SEC);
dispatch_after(time, dispatch_get_main_queue(), ^{
    NSLog(@"Hello, World!");
});
```

像这样使用`dispatch_after()`具有与非重复定时器相同的效果，但没有创建`NSTimer`对象的开销。

**总结**

在本章中，你学习了几种不同的代码执行方式。`performSelector:`方法家族提供了基本的消息路由功能，而定时器允许你在定时间隔内重复执行代码。运行循环是每个线程的基础，负责处理应用中的事件。线程及其伴随的线程安全问题允许你同时运行两段代码，而`NSOperationQueue`允许你将特定的工作块加入队列。最后，Grand Central Dispatch（GCD）使你能对代码的执行方式进行精细控制。完成本章后，你已经为编写高性能 iOS 应用做好了充分准备。我们使用 GCD 让 Twitter 示例的响应性更强，但其用户界面还有改进空间。在下一章中，我们将讨论如何实现出色的 iPhone 用户界面、渲染自定义图形以及实现动画效果。

