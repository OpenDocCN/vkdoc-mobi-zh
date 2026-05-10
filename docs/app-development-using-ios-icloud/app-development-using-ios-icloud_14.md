# 将记录保存到多个表

通常，根据你的应用数据持久化设计，你必须在一次事务中将多条记录保存到多个表中。由于 iCloud 回调是异步的，如果一次只保存一条记录，过程会变得非常繁琐，因为你需要等待第一条保存操作完成，才能调用下一条。

幸运的是，iOS 提供了一种巧妙的方式来处理这个问题，你可以使用一个块语句来保存多对多关系中的多条记录。在下面的示例中，我们将进行保存。

## 示例大纲

我们以一个多用户聊天为例，演示如何在多个 iCloud 表中持久化记录。我们的示例假设用户将有两种个人资料。第一种是对所有人可见的工作资料，第二种是仅用户本人可见的私人资料。此外，我们还会有一个所有用户均可使用的公共聊天表。我们将把用户的工作资料和聊天记录保存到两个不同的公共数据库中，而用户的个人资料则保存到私有数据库中。

请注意，此示例假设聊天创建的界面和所有逻辑都已存在（在第 11 章中，我们将学习一个双向聊天创建的示例），并且本示例仅关注多个 iCloud 表的持久化。该函数假设已提供必要的输入来执行保存操作。

#### 类定义

在下面的代码中，我们定义了一个继承自`NSObject`的聊天类。它导入了`UIKit`和`CloudKit`库，以确保所有必要的函数都可用于执行该操作。

```
import UIKit
import CloudKit
class Chat: NSObject {
```

### 保存函数定义

我们现在定义一个名为`saveRecordsToCloud`的函数，该函数将把三条记录保存到三个不同的表中。该函数接收工作资料、个人资料和聊天对象作为输入。该函数假设这些自定义类已预先定义。

由于我们要将记录保存到 iCloud 持久化数据库中，该函数还会在`container`变量中初始化 iCloud 的默认容器，并分别在`publicDatabase`和`privateDatabase`变量中初始化公共和私有数据库。

```
func saveRecordsToCloud(business: businessprofile, personal: personalProfile, chat: chat) {
    let container = CKContainer.default()
    let publicDatabase = container.publicCloudDatabase
    let privateDatabase = container.privateCloudDatabase
```



### 表记录初始化

在本节中，我们将用传递给函数的记录值来初始化记录类型。

下面的代码使用用户提供的值初始化了 `businessprofile` 记录类型。

我们首先使用 CloudKit 的 `CKRecord` 函数定义记录类型。在此示例中，`businessprofile` 是记录类型。如果该记录类型不存在，操作将首次创建它；如果已存在，则会将新记录追加到表中。

该记录包含三个参数：企业名称（`businessname`）、企业图片（`businessprofile`）以及企业描述（`businessdesc`）。

请注意，为了将图片存储到数据库中，我们需要使用图片的 `fileURL` 函数提取二进制数据，并将其传递给 `CKAsset` 类型。

```
let businessRecord = CKRecord(recordType: "businessproflle")
businessRecord["businessname"] = business.businessname
businessRecord["businessprofile"] = CKAsset(fileURL: business.businessprofile.fileURL!)
businessRecord["businessdesc"] = business.businessdesc
```

接下来是定义聊天表记录为 `chat` 的代码块。该记录包含三个值：发起者 ID，用于标识谁创建了聊天（`originatorid`）；实际聊天文本（`chattext`）；以及创建日期（`datecreated`），默认为系统当前日期和时间。

```
let chatRecord = CKRecord(recordType: "\chat")
chatRecord["originatorid"] = chat.originatorid
chatRecord["chattext"] = chat.chattext
chatRecord["datecreated"] = Date()
```

这是第三个也是最后一个记录定义块。记录类型为 `personalprofile`。此记录将存储在私有数据库中。记录被分配了三个值：用户的个人资料图片、用户名称以及聊天文本，该文本将是聊天记录中的最新聊天内容。

```
let personalRecord = CKRecord(recordType: "personalprofile")
personalRecord["profile"] = CKAsset(fileURL: personal.profile.fileURL!)
personalRecord["name"] = personal.name
personalChatRecord["lastchat"] = chat.chattext
```

### 保存操作

在下面的代码中，我们定义了公共数据库的保存操作。变量名为 `publicSaveOperation`（注意它是一个 `let` 变量，因为我们只使用它一次），它使用了 `CKModifyRecordsOperation` 系统函数。该方法接受一个待保存记录的数组作为其参数（标签为 `recordsToSave`）的输入。

我们将两个公共数据库记录 `businessRecord` 和 `chatRecord` 提供给此操作。

```
let publicSaveOperation = CKModifyRecordsOperation(recordsToSave: [businessRecord, chatRecord])
publicSaveOperation.savePolicy = .changedKeys
```

接下来，我们异步等待操作完成。`modifyRecordsResultBlock` 在最新的 iOS 版本中可用，它提供了一个结果 `enum`（请参阅第 2 章）。

如果操作成功，我们现在希望将用户资料保存到用户的私有数据库中。请注意，`recordsToSave` 可以接受一个数组；但在我们的案例中，我们只提供 `personalRecord` 作为唯一要保存的记录。

我们再次异步等待私有数据库操作完成。这次如果成功，我们将退出函数（`self.group.leave()`）。如果失败，我们将在控制台打印一条消息。

`privateDatabase.add(privateSaveOperation)` 和 `publicDatabase.add(publicSaveOperation)` 会将函数排入队列以在后台执行。

```
publicSaveOperation.modifyRecordsResultBlock = { result in
switch result {
case .success:
self.syncQueue.async {
let privateSaveOperation = CKModifyRecordsOperation(recordsToSave: [personalRecord])
privateSaveOperation.savePolicy = .changedKeys
privateSaveOperation.modifyRecordsResultBlock = { result in
switch result {
case .success:
self.syncQueue.async {
self.group.leave()
}
case .failure(let error):
print("将记录保存到私有数据库时出错：\(error.localizedDescription)")
// 处理错误情况
}
}
privateDatabase.add(privateSaveOperation)
}
// 处理任何后续操作或界面更新
case .failure(let error):
print("保存记录时出错：\(error.localizedDescription)")
// 处理错误情况
}
}
publicDatabase.add(publicSaveOperation)
}
```

### 完整代码

```
import UIKit
import CloudKit
class Chat: NSObject {
func saveRecordsToCloud(business:businessprofile, personal:personalProfile, chat:chat){
let container = CKContainer.default()
let publicDatabase = container.publicCloudDatabase
let privateDatabase = container.privateCloudDatabase
let businessRecord = CKRecord(recordType: "businessproflle")
businessRecord["businessname"] = business.businessname
businessRecord["businessprofile"] = CKAsset(fileURL: business.businessprofile.fileURL!)
businessRecord["businessdesc"] = business.businessdesc
let chatRecord = CKRecord(recordType: "\chat")
chatRecord["originatorid"] = chat.originatorid
chatRecord["chattext"] = chat.chattext
chatRecord["datecreated"] = Date()
let personalRecord = CKRecord(recordType: "personalprofile")
personalRecord["profile"] = CKAsset(fileURL: personal.profile.fileURL!)
personalRecord["name"] = personal.name
personalChatRecord["lastchat"] = chat.chattext
let publicSaveOperation = CKModifyRecordsOperation(recordsToSave: [businessRecord, chatRecord])
publicSaveOperation.savePolicy = .changedKeys
publicSaveOperation.modifyRecordsResultBlock = { result in
switch result {
case .success:
self.syncQueue.async {
let privateSaveOperation = CKModifyRecordsOperation(recordsToSave: [personalRecord])
privateSaveOperation.savePolicy = .changedKeys
privateSaveOperation.modifyRecordsResultBlock = { result in
switch result {
case .success:
self.syncQueue.async {
self.group.leave()
}
case .failure(let error):
print("将记录保存到私有数据库时出错：\(error.localizedDescription)")
// 处理错误情况
}
}
privateDatabase.add(privateSaveOperation)
}
// 处理任何后续操作或界面更新
case .failure(let error):
print("保存记录时出错：\(error.localizedDescription)")
// 处理错误情况
}
}
publicDatabase.add(publicSaveOperation)
}
}
```

