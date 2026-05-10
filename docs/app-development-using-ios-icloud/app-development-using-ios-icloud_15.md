# 10. 推送通知

推送通知是应用程序的重要组成部分。当应用程序状态发生变化时，您通常希望通知用户，以便用户采取适当行动。在我们的聊天应用中，当用户向另一用户发送文本时，通知将帮助对方用户响应变化或采取适当行动。



## 应用委托注册

实现推送通知的第一步是注册通知。iCloud iOS 工具包提供了一种数据库订阅方法，通过该方法我们可以使用唯一标识符进行订阅注册。一旦该订阅被持久化并允许发送通知，它就会为应用启用推送通知功能。

打开您的应用委托类，并添加以下函数。该方法执行以下操作：

- 获取默认容器公共数据库的引用

```
let container = CKContainer.default()
let publicDatabase = container.publicCloudDatabase
let subscription = CKDatabaseSubscription(subscriptionID: "mySubscription")
```

- 创建一个类型为 `CKDatabaseSubscription`、名称为 `"mySubscription"` 的订阅变量

```
let subscription = CKDatabaseSubscription(subscriptionID: "mySubscription")
```

- 创建一个通知信息对象，并将其属性 `shouldSendContentAvailable` 设置为 `true`

```
let notificationInfo = CKSubscription.NotificationInfo()
notificationInfo.shouldSendContentAvailable = true
```

- 将通知信息对象赋值给订阅变量

```
subscription.notificationInfo = notificationInfo
```

- 使用订阅对象更新数据库

```
let operation = CKModifySubscriptionsOperation(subscriptionsToSave: [subscription], subscriptionIDsToDelete: [])
operation.modifySubscriptionsResultBlock = { result in
    switch result {
    case .success:
        print("Succesfully Subscribed")
    case .failure(let error):
        print("Error registering for notifications: \(error.localizedDescription)")
    }
}
publicDatabase.add(operation)
```

```
func registerForNotifications() {
    let container = CKContainer.default()
    let publicDatabase = container.publicCloudDatabase
    let subscription = CKDatabaseSubscription(subscriptionID: "mySubscription")
    let notificationInfo = CKSubscription.NotificationInfo()
    notificationInfo.shouldSendContentAvailable = true
    subscription.notificationInfo = notificationInfo
    let operation = CKModifySubscriptionsOperation(subscriptionsToSave: [subscription], subscriptionIDsToDelete: [])
    operation.modifySubscriptionsResultBlock = { result in
        switch result {
        case .success:
            print("Succesfully Subscribed")
        case .failure(let error):
            print("Error registering for notifications: \(error.localizedDescription)")
        }
    }
    publicDatabase.add(operation)
}
```

### 设置订阅

接下来，我们需要为变更设置一个订阅。您可以在记录更新或创建之后，将以下方法添加到任意模态类中。在我们的示例中，我们将把它添加到聊天模型类中。该功能执行以下操作：

- 访问 iCloud 环境中的容器和公共数据库。

```
let container = CKContainer.default()
let publicDatabase = container.publicCloudDatabase
```

- 创建一个查询订阅。请注意，查询订阅需要记录类型名称和订阅名称（名称必须与您之前创建的保持一致），以及用于告知系统需要在创建或更新该记录类型时提供通知的选项。此外，您还可以为查询提供条件。在我们的案例中，我们将其应用于所有记录，因此 `NSPredicate` 的值为 `True`。

```
let subscription = CKQuerySubscription(
    recordType: "\(tablename)",
    predicate: NSPredicate(value: true),
    subscriptionID: "mySubscription",
    options: [.firesOnRecordCreation, .firesOnRecordUpdate]
)
```

- 创建带有自定义文本的通知信息。此文本将显示在用户的屏幕上。

```
let notificationInfo = CKSubscription.NotificationInfo()
notificationInfo.alertBody = "You have a New Chat"
subscription.notificationInfo = notificationInfo
```

- 最后，保存订阅。

```
publicDatabase.save(subscription) { (subscription, error) in
    if let error = error {
        print("Error saving subscription: \(error.localizedDescription)")
        self.group.leave()
    } else {
        print("Subscription saved successfully!")
        self.group.leave()
    }
}
```

这将触发通知流程。所有订阅了该记录类型的用户都将收到通知对象中指定的警报。

```
func setSubscription(tablename: String, chattext: String, originatorname: String) {
    // 为数据库表设置一个 CKSubscription
    let container = CKContainer.default()
    let publicDatabase = container.publicCloudDatabase
    let subscription = CKQuerySubscription(
        recordType: "\(tablename)",
        predicate: NSPredicate(value: true),
        subscriptionID: "mySubscription",
        options: [.firesOnRecordCreation, .firesOnRecordUpdate]
    )
    let notificationInfo = CKSubscription.NotificationInfo()
    notificationInfo.alertBody = "你有一条新聊天"
    subscription.notificationInfo = notificationInfo
    publicDatabase.save(subscription) { (subscription, error) in
        if let error = error {
            print("保存订阅时出错: \(error.localizedDescription)")
            self.group.leave()
        } else {
            print("订阅保存成功！")
            self.group.leave()
        }
    }
}
```

