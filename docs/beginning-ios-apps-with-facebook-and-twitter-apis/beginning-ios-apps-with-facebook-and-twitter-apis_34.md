# 核心数据推文获取与删除

当需要从 Core Data 模型中获取推文时，我们使用 Core Data 类 `NSFetchRequest`。`NSFetchRequest` 接收一个实体描述（本例中为 `Tweet`）和一个托管对象上下文。接着，我们通过调用托管对象上下文的 `executeFetchRequest:` 方法，并传入初始化好的 `NSFetchRequest` 对象，从 Core Data 模型中检索推文。该方法随后返回推文数组。请注意，如果你希望对托管对象上下文中的提取结果进行排序，则需要为 `NSFetchRequest` 创建并设置一个 `NSSortDescriptor`。在本例中，我们初始化了一个 `NSSortDescriptor`，它将根据推文的 `id` 属性值，按降序对返回的推文数组进行排序：

```
- (NSArray*)tweets
{
    NSMutableArray *tweets = nil;

    @synchronized(self) {
        NSFetchRequest *request = [[NSFetchRequest alloc] init];
        NSEntityDescription *entity =
                                           [NSEntityDescription entityForName:@"Tweet"
                                inManagedObjectContext:self.managedObjectContext];
        [request setEntity:entity];

        NSSortDescriptor *sortDescriptor = [[NSSortDescriptor alloc] initWithKey:@"id"
                                                                       ascending:NO];
        NSArray *sortDescriptors = [[NSArray alloc] initWithObjects:sortDescriptor, nil];
        [request setSortDescriptors:sortDescriptors];
        [sortDescriptors release];
        [sortDescriptor release];

        NSError *error = nil;
        NSMutableArray *mutableFetchResults =
                 [[self.managedObjectContext executeFetchRequest:request error:&error] mutableCopy];
        if (mutableFetchResults == nil) {
            // 处理错误
        }

        tweets = [mutableFetchResults retain];
        [mutableFetchResults release];
        [request release];
    }

    return tweets;
}
```

要删除 Core Data 模型中的所有推文，我们需获取当前存储在模型中的所有推文，逐一循环遍历，告知托管对象上下文删除指定推文，然后保存托管对象上下文以将结果提交到磁盘：

```
- (void)deleteTweets
{
    @synchronized(self) {
        NSArray *tweets = [self tweets];
        for (Tweet *tweet in tweets) {
            [self.managedObjectContext deleteObject:tweet];
        }

        // 提交更改。

        NSError *error = nil;

        if (![self.managedObjectContext save:&error]) {
            // 处理错误。
        }
    }
}
```

### 结论

本章中，我们涉及了一些真正有趣的新领域，包括上传照片以及使用 Core Data 为应用程序构建简单数据模型。构建数据模型还有其他可选方案（例如 SQLite），因此我们建议你探索其他方式来存储数据，以便在应用内进行离线或快速检索。请注意，你可能需要在模型中构建一些智能机制来限制应用占用的存储空间。例如，你可能只想存储一定数量的近期推文，或者只存储过去 12 小时内的推文。

下一章中，我们将讨论不同的基于位置的场景；解释如何将位置信息整合到你的应用中，以及如何与 Facebook 和 Twitter 的社交网络数据结合；同时，我们将通过设置一个可在应用内任意位置使用的独立控制器，继续完善我们整合这些服务的方式。

