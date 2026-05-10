# 12. 用户之间的对话 – 模态定义

## 定义模态类

为了模拟上述表，我们的聊天应用需要创建相应的模态类。通常，模态类与 iCloud 表之间存在一对一的关系，但有时也会存在一对多的关系。我们将在以下部分详细探讨这一点。每个类都将拥有各自的 CRUD 函数，以实现 iCloud 操作，例如创建记录和检索记录。

### `Profile` 模态类

我们编写的第一个模态类是用户个人资料。以下代码确认了 `UIKit`（用于 UI 定义的默认框架）和 `CloudKit`（iCloud 操作所需）模块，并定义了一个自定义类名 `Profile`，其类型为 `NSObject`。

```swift
import UIKit
import CloudKit
class Profile: NSObject {
```

在此类中，我们将执行许多 iCloud 异步操作。为了在线程上运行代码并等待方法操作完成，我们定义了一个调度组和一个同步队列。

我们还定义了一个名为 `profile` 的类级别对象，其类型为 `Profile`（注意区分大小写的定义）。

```swift
let group = DispatchGroup()
let syncQueue = DispatchQueue(label: "com.domain.app.sections")
var profile:Profile?
```

下面定义的变量模拟了 iCloud 中的 `profile` 表。除了自定义变量外，我们还定义了一个名为 `record` 的变量，其类型为 `CKRecord`；实例化后，它将拥有对记录 ID 的引用。

为自定义变量定义了一个 `init` 方法，用于初始化任何类对象，这将是实例化类变量所需的参数。`init` 方法将接收用户提供的参数，并将其赋值给 `Profile` 类的对象。

```swift
var record:CKRecord!
var userid:String!
var personalprofile:CKAsset!
var name: String!
var businessname:String!
var businesstype:String!
init(userid:String, personalprofile:CKAsset, name:String, businessname:String, businesstype:String) {
self.userid = userid
self.personalprofile = personalprofile
self.name = name
self.businessname = businessname
self.businesstype = businesstype
}
```

#### 保存个人资料方法

此方法将用户个人资料保存到 iCloud 的 `profile` 表中。该方法接收一个 `profile` 对象作为输入。

```swift
func saveProfile(profile:Profile){
```

`profile` 表位于 iCloud 公共云数据库中。以下代码获取公共云数据库的引用，定义一个名为 `aRecord` 的记录类型（记录类型为 `profile`），并将用户提供的个人资料数据赋值给 `profile` 记录的相应字段（用户 ID、个人资料图片、用户名、业务名称、业务类型）。

```swift
let publicDatabase = CKContainer.default().publicCloudDatabase
let aRecord = CKRecord(recordType: "profile")
aRecord["userid"] = profile.userid
aRecord["personalprofile"] = profile.personalprofile
aRecord["name"] = profile.name
aRecord["businessname"] = profile.businessname
aRecord["businesstype"] = profile.businesstype
```

下面的代码现在尝试将记录持久化到 iCloud 存储库的 `profile` 表中。如果成功，它将返回保存的记录，该记录被存储到类级别变量 `profile` 中。请注意，我们在主线程上等待，一旦收到记录作为返回值，我们就离开该线程并继续处理其他任务。如果无法保存记录，控制台将显示错误。

你可能会想，为什么我们不能直接将用户提供的值赋值给 `profile` 对象，而要等待 iCloud 提供相同的值。这是因为一旦记录被保存，我们就能获得已保存记录的记录 ID，这对于执行其他 CRUD 操作（如更新、删除或查询）至关重要。

```swift
publicDatabase.save(aRecord, completionHandler: { (record, error) -> Void in
DispatchQueue.main.async {
if let error = error {
print(error)
}
else {
self.syncQueue.async {
self.profile = Profile(userid: record!.object(forKey: "userid")! as! String, personalprofile: record!.object(forKey: "personalprofile")! as! CKAsset, name: record!.object(forKey: "name")! as! String, businessname: record!.object(forKey: "businessname")! as! String, businesstype: record!.object(forKey: "businesstype")! as! String)
self.profile?.record = record
self.group.leave()
}
}
}
})
}
}
```



`Profile` 模态类的完整代码如下：

```swift
import UIKit
import CloudKit

class Profile: NSObject {
    let group = DispatchGroup()
    let syncQueue = DispatchQueue(label: "com.domain.app.sections")
    var profile: Profile?
    var record: CKRecord!
    var userid: String!
    var personalprofile: CKAsset!
    var name: String!
    var businessname: String!
    var businesstype: String!

    init(userid: String, personalprofile: CKAsset, name: String, businessname: String, businesstype: String) {
        self.userid = userid
        self.personalprofile = personalprofile
        self.name = name
        self.businessname = businessname
        self.businesstype = businesstype
    }

    // MARK: - 保存例程
    func saveProfile(profile: Profile) {
        let publicDatabase = CKContainer.default().publicCloudDatabase
        let aRecord = CKRecord(recordType: "profile")
        aRecord["userid"] = profile.userid
        aRecord["personalprofile"] = profile.personalprofile
        aRecord["name"] = profile.name
        aRecord["businessname"] = profile.businessname
        aRecord["businesstype"] = profile.businesstype

        publicDatabase.save(aRecord, completionHandler: { (record, error) -> Void in
            DispatchQueue.main.async {
                if let error = error {
                    print(error)
                } else {
                    self.syncQueue.async {
                        self.profile = Profile(
                            userid: record!.object(forKey: "userid")! as! String,
                            personalprofile: record!.object(forKey: "personalprofile")! as! CKAsset,
                            name: record!.object(forKey: "name")! as! String,
                            businessname: record!.object(forKey: "businessname")! as! String,
                            businesstype: record!.object(forKey: "businesstype")! as! String
                        )
                        self.profile?.record = record
                        self.group.leave()
                    }
                }
            }
        })
    }
}
```

## `Business` 模态类

`Business` 模态类在我们的 iCloud 仓库中不代表一张表。定义该类是为了提供所有业务类型，作为 `Profile` 类中业务类型字段的输入。当前，我们将其作为类的一部分进行持久化（你将在程序的后续部分看到这一点）。在你的实现中，你可以选择将其持久化到 iCloud 表中，并将其作为输入辅助表来检索。

该类包含三个字段：业务类型、业务描述，以及一个提供业务类型视觉描述的图像。`init` 方法将帮助初始化该类的对象。

```swift
import UIKit

class Business: NSObject {
    var businesstype: String!
    var businessdesc: String!
    var imagename: String!

    init(businesstype: String, businessdesc: String, imagename: String) {
        self.businesstype = businesstype
        self.businessdesc = businessdesc
        self.imagename = imagename
    }
}
```

## `Contact` 模态类

`Contact` 模态类在我们的 iCloud 仓库中没有表表示。它提供了我们可以在 `Profile` 类对象上执行的所有 CRUD 函数。

在下面的代码中，我们定义了 `Contact` 类，并除了默认的 `UIKit` 模块外，还导入了 `CloudKit` 以进行云操作。

```swift
import UIKit
import CloudKit

class Contact: NSObject {
```

由于我们将执行异步 iCloud 操作，因此定义了队列和组。下面还定义了三个类级别的变量：

- `profiles`，一个 `Profile` 类型的数组
- `profileExists`，一个布尔变量，指示 `Profile` 是否存在
- `profile`，一个 `Profile` 类型的对象

```swift
let group = DispatchGroup()
let syncQueue = DispatchQueue(label: "com.domain.app.sections")
var profiles: [Profile] = []
var profileExists = false
var profile: Profile?
```

#### `getContacts` 方法

此方法从 iCloud 仓库中检索所有具有相同业务类型的可用 `Profile`。该方法接受 `businessType` 作为输入，执行以下功能：

```swift
func getContacts(businessType: String) {
```

该函数将所有 `Profile` 存储到之前定义的 `profiles` 数组中。我们清空该数组以避免在函数被调用两次时出现重复条目。我们通过设置条件来设置查询谓词，仅当字段 `businesstype` 等于用户提供的 `businessType` 值时，才检索数据。

然后，将该查询分配给一个 `CKQueryOperation` 类型的 `operation`，该操作将在公共云数据库（`profile` 表所在的位置）上执行。我们还设置了一些操作配置，如果请求耗时超过 10 秒，则超时。

```swift
profiles.removeAll()
let pred = NSPredicate(format: "businesstype == %@", businessType)
let query = CKQuery(recordType: "profile", predicate: pred)
let operation = CKQueryOperation(query: query)
CKContainer.default().publicCloudDatabase.add(operation)

let operationConfiguration = CKOperation.Configuration()
operationConfiguration.timeoutIntervalForRequest = 10
operationConfiguration.timeoutIntervalForResource = 10
operation.configuration = operationConfiguration
```

`recordMatchedBlock` 在异步方法完成后被调用。它返回一个 `result` 对象，该对象是一个枚举。我们现在需要解包 `result` 枚举，该枚举对于每条记录包含两个数据元素：`success` 和 `failure`。`case` 语句将帮助我们在这两个数据元素之间切换。如果成功，我们将返回的对象值存储到类变量 `profile` 中，并将其追加到类级别定义的数组变量 `profiles` 中。

如果失败，我们将在控制台打印错误，并将 `profileExists` 变量设置为 `false`。

```swift
operation.recordMatchedBlock = { recordID, result in
    switch result {
    case .success(let record):
        let profile = Profile(
            userid: record["userid"] as! String,
            personalprofile: record["personalprofile"] as! CKAsset,
            name: record["name"] as! String,
            businessname: record["businessname"] as! String,
            businesstype: record["businesstype"] as! String
        )
        profile.record = record
        self.profiles.append(profile)
    case .failure(let error):
        print(error.localizedDescription)
        // 处理表不存在或存在错误的情况
        // self.profileExists = false
    }
}
```

在 `queryResultBlock` 中，我们再次对 `enum` 变量 `result` 进行切换。如果成功，我们返回主线程；如果失败，则将 `profileExists` 变量设置为 `false` 并退出线程。

```swift
operation.queryResultBlock = { result in
    DispatchQueue.main.async {
        switch result {
        case .failure(let error):
            debugPrint(error)
            // 处理查询结果失败
            // self.profileExists = false
            self.syncQueue.async {
                self.group.leave()
            }
        case .success(_):
            // 处理查询结果成功
            self.syncQueue.async {
                self.group.leave()
            }
        }
    }
}
}
```

#### `getICloudUserID` 方法

iCloud 中的每个用户都有一个唯一的云 ID。我们使用它来唯一标识我们聊天应用程序中的每个用户。下面的函数从默认容器中获取用户 ID。

请注意，此方法中有一个完成处理器，带有两个输入变量：第一个接受一个字符串值（用户 ID），第二个接受一个错误（如果存在）。如果成功，我们将用户 ID 传递给完成处理器，并将错误变量设置为 `nil`，然后返回调用 `getICloudUserID` 的方法。如果失败，我们将用户 ID 的输入提供为 `nil`，并将错误返回给调用方法。

```swift
func getICloudUserID(completion: @escaping (String?, Error?) -> Void) {
    let container = CKContainer.default()
    container.fetchUserRecordID { (recordID, error) in
        if let error = error {
            // 处理错误
            completion(nil, error)
        } else if let recordID = recordID {
            let userID = recordID.recordName
            completion(userID, nil)
        }
    }
}
```



##### 检查用户档案是否存在

此方法以用户 ID 作为输入，检查该用户是否存在于 `profile` iCloud 仓库中。根据 `userid` 字段等于方法提供的 `userID` 变量输入这一条件，构建查询对象。然后，该查询对象被添加到 `CKQueryOperation` 类型的操作变量中。接着添加一些操作超时配置，最后将该操作添加到公共云数据库以执行。

```
func checkProfileExist(userID:String) {
let pred = NSPredicate(format: "userid == %@", userID)
let query = CKQuery(recordType: "profile", predicate: pred)
let operation = CKQueryOperation(query: query)
let operationConfiguration = CKOperation.Configuration()
operationConfiguration.timeoutIntervalForRequest = 10
operationConfiguration.timeoutIntervalForResource = 10
operation.configuration = operationConfiguration
CKContainer.default().publicCloudDatabase.add(operation)
```

`recordMatchedBlock` 代码块在操作异步完成后被触发。`enum` 对象被遍历以获取 `success` 或 `failure` 结果。成功时，我们将类变量 `profileExists` 设置为 true，并将返回的记录赋值给类变量 `profile`。失败时，我们将 `profileExists` 设置为 false。

```
operation.recordMatchedBlock = { recordID, result in
switch result {
case .success(let record):
self.profileExists = true
self.profile = Profile(userid: record["userid"] as! String, personalprofile: record["personalprofile"] as! CKAsset, name: record["name"] as! String, businessname: record["businessname"] as! String, businesstype: record["businesstype"] as! String)
self.profile!.record = record
case .failure(let error):
print(error.localizedDescription)
// 处理表不存在或出现错误的情况
self.profileExists = false
}
}
```

在 `queryResultBlock` 中，我们再次遍历 `enum` 结果对象。失败时，我们将 `profileExists` 设置为 false 并返回到主线程。成功时，我们让线程直接返回到主线程。

```
operation.queryResultBlock = { result in
DispatchQueue.main.async {
switch result {
case .failure(let error):
debugPrint(error)
// 处理查询结果失败
self.profileExists = false
self.syncQueue.async {
self.group.leave()
}
case .success(_):
// 处理查询结果成功
self.syncQueue.async {
self.group.leave()
}
}
}
}
}
}
```

### 完整代码

以下是 `Contact` 类的完整代码：

```
import UIKit
import CloudKit
class Contact: NSObject {
let group = DispatchGroup()
let syncQueue = DispatchQueue(label: "com.domain.app.sections")
var profiles:[Profile] = []
var profileExists = false
var profile:Profile?
func getContacts(businessType:String) {
profiles.removeAll()
let pred = NSPredicate(format: "businesstype == %@", businessType)
let query = CKQuery(recordType: "profile", predicate: pred)
let operation = CKQueryOperation(query: query)
CKContainer.default().publicCloudDatabase.add(operation)
let operationConfiguration = CKOperation.Configuration()
operationConfiguration.timeoutIntervalForRequest = 10
operationConfiguration.timeoutIntervalForResource = 10
operation.configuration = operationConfiguration
operation.recordMatchedBlock = { recordID, result in
switch result {
case .success(let record):
let profile = Profile(userid: record["userid"] as! String, personalprofile: record["personalprofile"] as! CKAsset, name: record["name"] as! String, businessname: record["businessname"] as! String,  businesstype: record["businesstype"] as! String)
profile.record = record
self.profiles.append(profile)
case .failure(let error):
print(error.localizedDescription)
// 处理表不存在或出现错误的情况
//self.profileExists = false
}
}
operation.queryResultBlock = { result in
DispatchQueue.main.async {
switch result {
case .failure(let error):
debugPrint(error)
// 处理查询结果失败
//self.profileExists = false
self.syncQueue.async {
self.group.leave()
}
case .success(_):
// 处理查询结果成功
self.syncQueue.async {
self.group.leave()
}
}
}
}
}
func getICloudUserID(completion: @escaping (String?, Error?) -> Void) {
let container = CKContainer.default()
container.fetchUserRecordID { (recordID, error) in
if let error = error {
// 处理错误
completion(nil, error)
} else if let recordID = recordID {
let userID = recordID.recordName
completion(userID, nil)
}
}
}
func checkProfileExist(userID:String) {
let pred = NSPredicate(format: "userid == %@", userID)
let query = CKQuery(recordType: "profile", predicate: pred)
let operation = CKQueryOperation(query: query)
let operationConfiguration = CKOperation.Configuration()
operationConfiguration.timeoutIntervalForRequest = 10
operationConfiguration.timeoutIntervalForResource = 10
operation.configuration = operationConfiguration
CKContainer.default().publicCloudDatabase.add(operation)
operation.recordMatchedBlock = { recordID, result in
switch result {
case .success(let record):
self.profileExists = true
self.profile = Profile(userid: record["userid"] as! String, personalprofile: record["personalprofile"] as! CKAsset, name: record["name"] as! String, businessname: record["businessname"] as! String, businesstype: record["businesstype"] as! String)
self.profile!.record = record
case .failure(let error):
print(error.localizedDescription)
// 处理表不存在或出现错误的情况
self.profileExists = false
}
}
operation.queryResultBlock = { result in
DispatchQueue.main.async {
switch result {
case .failure(let error):
debugPrint(error)
// 处理查询结果失败
self.profileExists = false
self.syncQueue.async {
self.group.leave()
}
case .success(_):
// 处理查询结果成功
self.syncQueue.async {
self.group.leave()
}
}
}
}
}
}
```

### 会话模型类

会话模型类模拟了 iCloud 仓库中的会话表。根据谁与谁在交谈，我们在仓库中会有唯一的聊天表名称。表名称由发起者（发起会话的一方）的名称和接收者（聊天内容发送给的一方）的名称组合而成。

定义了一个名为 `Conversation` 的类，类型为 `NSObject`，它继承了两个模块：`CloudKit`（用于 iCloud 操作）和 `UIKit`（用于 UI 定义）。

```
import UIKit
import CloudKit
class Conversation: NSObject {
```

为 iCloud 的异步操作定义了队列和组。

```
let group = DispatchGroup()
let syncQueue = DispatchQueue(label: "com.domain.app.sections")
```

定义了描述会话表字段的类变量。它有两个主要变量：

*   `chattext`，用户提供的聊天文本
*   `originatorid`，提供文本的用户 ID

我们有两个 `init` 方法：一个接受用户提供的输入并将其赋值给类对象，另一个是空构造函数。空构造函数帮助我们创建类的对象，以便在无需提供任何输入值的情况下调用类的方法。

```
var record:CKRecord!
var chattext:String!
var originatorid:String!
init(chattext:String!, originatorid:String!) {
self.chattext = chattext
self.originatorid = originatorid
}
override init() {
}
```



#### 获取对话方法

此函数用于检索聊天发起者与特定接收用户之间的所有聊天记录。该方法以发起者和接收者的名称作为输入，并带有一个完成处理程序，该处理程序返回一个 `Conversation` 类数组对象，以及在出现错误时返回的错误信息。

```
func getChats(receipentname:String, originatorname:String, completion:@escaping ([Conversation], Error?) -> Void){
```

在下面的代码中，我们定义了一个名为 `chats` 的方法级变量，它是一个 `Conversation` 类型的数组。由于记录类型是动态的，我们通过组合发起者和接收者的名称来构建它。

```
var chats:[Conversation] = []
var recordType = "\(originatorname.replacingOccurrences(of: " ", with: ""))_\(receipentname.replacingOccurrences(of: " ", with: ""))"
recordType = recordType.lowercased()
```

我们想要获取所有对话。这个虚拟变量在创建聊天时被设定，其值始终为 1。这将帮助我们获取所有对话。我们按创建日期对结果进行排序。这样，最近创建的记录就会排在顶部，同时设置操作配置并将结果限制为前 2000 条聊天记录。

请注意，你也可以实现批量检索，即先检索固定数量的对话。当用户执行下拉手势等操作时，你可以检索下一批数据。在我们的代码中，我们只是简单地检索了前 2000 条记录。

```
let pred = NSPredicate(format: "dummy == %@", "1")
let query = CKQuery(recordType: "\(recordType)", predicate: pred)
query.sortDescriptors = [NSSortDescriptor(key: "datecreated", ascending: true)]
let operation = CKQueryOperation(query: query)
CKContainer.default().publicCloudDatabase.add(operation)
let operationConfiguration = CKOperation.Configuration()
operationConfiguration.timeoutIntervalForRequest = 10
operationConfiguration.timeoutIntervalForResource = 10
operation.configuration = operationConfiguration
operation.resultsLimit = 2000
```

`recordMatchedBlock` 在操作完成时被调用。在 `success` 情况下，遍历 `enum` 对象，我们将聊天对象设置为检索到的值，并将其追加到函数级别的 `chats` 变量中。在 `failure` 情况下，我们将错误打印到控制台。

```
operation.recordMatchedBlock = { recordID, result in
switch result {
case .success(let record):
let chat = Conversation(chattext: record["chattext"] as? String, originatorid: record["originatorid"] as? String)
chat.record = record
chats.append(chat)
case .failure(let error):
print(error.localizedDescription)
}
}
```

最后，在 `queryResultBlock` 中，在成功的情况下，我们返回带有 `chats` 数组对象和 `nil` 的完成处理程序；在失败的情况下，返回 `nil` 和错误信息。

```
operation.queryResultBlock = { result in
DispatchQueue.main.async {
switch result {
case .failure(let error):
debugPrint(error)
print(error)
completion(nil, error)
case .success(_):
completion(chats, nil)
}
}
}
}
```

#### 保存对话方法

此方法在首次发起聊天时将对话保存到云端。它接收发起者名称、接收者名称、发起者和接收者的个人资料以及实际的聊天文本。

```
func saveNewChatRecordsToCloud(receipentname:String, originatorname:String, receipentprofile:CKAsset, originatorprofile:CKAsset, lastchat:String) {
```

在此方法中，我们将保存两条记录到公共云数据库，以及一条记录到私有云数据库。下面的代码定义了数据库变量以及如前所述的对话表名。

```
let container = CKContainer.default()
let publicDatabase = container.publicCloudDatabase
let privateDatabase = container.privateCloudDatabase
var recordType = "\(originatorname.replacingOccurrences(of: " ", with: ""))_\(receipentname.replacingOccurrences(of: " ", with: ""))"
recordType = recordType.lowercased()
```

下面的代码定义了以下三条记录：

*   `businessChatRecord`：这是为每个用户业务动态定义的。表以业务名称命名。它存储聊天发起者的名称、发起者个人资料、便于查询检索的虚拟值 1，以及最后一条聊天对话。此记录将存储在公共数据库中。

*   `chatRecord`：这是对话表，存储两个用户（发起者和接收者）之间的所有对话。它有四个字段：发起者用户 ID、聊天文本、聊天创建日期，以及默认值为 1 的虚拟字段（便于检索记录）。此记录将存储在公共数据库中。

*   `personalChatRecord`：此记录将存储在 `chattables` 中。它包含对话表名和接收者名称、个人资料图片、默认值为 1 的虚拟变量，以及聊天文本。此记录将存储在私有数据库中。

为 `businessChatRecord` 和 `personalChatRecord` 创建了一个公共云数据库的操作。

```
let businessChatRecord = CKRecord(recordType: "\(receipentname.replacingOccurrences(of: " ", with: "").lowercased())")
businessChatRecord["chattablename"] =  "\(recordType)"
businessChatRecord["originatorname"] = originatorname
businessChatRecord["originatorprofile"] = CKAsset(fileURL: originatorprofile.fileURL!)
businessChatRecord["dummy"] = "1"
businessChatRecord["lastchat"] = lastchat
let chatRecord = CKRecord(recordType: "\(recordType)")
chatRecord["originatorid"] = self.originatorid
chatRecord["chattext"] = self.chattext
chatRecord["datecreated"] = Date()
chatRecord["dummy"] = "1"
let personalChatRecord = CKRecord(recordType: "chattables")
personalChatRecord["chattablename"] = "\(recordType)"
personalChatRecord["receipentprofile"] = CKAsset(fileURL: receipentprofile.fileURL!)
personalChatRecord["receipentname"] = receipentname
personalChatRecord["dummy"] = "1"
personalChatRecord["lastchat"] = lastchat
let publicSaveOperation = CKModifyRecordsOperation(recordsToSave: [businessChatRecord, chatRecord])
publicSaveOperation.savePolicy = .changedKeys
```

执行下面的代码来更新公共云端的 iCloud 仓库。成功时，我们为 `personalChatRecord` 数据创建一个私有云操作并执行它。成功时，我们退出组。失败时，我们将错误打印到控制台。

```
publicSaveOperation.modifyRecordsResultBlock = { result in
switch result {
case .success:
self.syncQueue.async {
let privateSaveOperation = CKModifyRecordsOperation(recordsToSave: [personalChatRecord])
privateSaveOperation.savePolicy = .changedKeys
privateSaveOperation.modifyRecordsResultBlock = { result in
switch result {
case .success:
self.syncQueue.async {
self.group.leave()
}
// 处理任何后续操作或 UI 更新
case .failure(let error):
print("保存记录到私有数据库时出错: \(error.localizedDescription)")
// 处理错误情况
}
}
privateDatabase.add(privateSaveOperation)
}
// 处理任何后续操作或 UI 更新
case .failure(let error):
print("保存记录时出错: \(error.localizedDescription)")
// 处理错误情况
}
}
publicDatabase.add(publicSaveOperation)
}
```



##### 保存对话的云端第二种方法

当两个用户之间的聊天已经存在时，会调用此方法。它接收发起者和接收者的姓名作为参数。

首先建立对公共云端的引用，并根据用户提供的输入动态创建记录类型。然后创建一条对话类型的记录，并使用发起者用户 ID、聊天文本以及一个文本值设置为 1 的虚拟变量来初始化其字段。

```
func saveNewChatToCloud(tablename: String, receipentname:String, originatorname:String) {
    let container = CKContainer.default()
    let publicDatabase = container.publicCloudDatabase
    var recordType = "\(originatorname.replacingOccurrences(of: " ", with: ""))_\(receipentname.replacingOccurrences(of: " ", with: ""))"
    recordType = recordType.lowercased()
    let chatRecord = CKRecord(recordType: "\(recordType)")
    chatRecord["originatorid"] = self.originatorid
    chatRecord["chattext"] = self.chattext
    chatRecord["datecreated"] = Date()
    chatRecord["dummy"] = "1"
```

在下面的代码中，我们将执行 `CKModifyRecordsOperation` 并将聊天记录保存到表中。如果成功，我们会调用一个自定义函数 `setSubscription`。这是为了通知接收者用户有新聊天被创建。如果失败，我们会在控制台打印错误信息。

```
let publicSaveOperation = CKModifyRecordsOperation(recordsToSave: [chatRecord])
publicSaveOperation.savePolicy = .changedKeys
publicSaveOperation.modifyRecordsResultBlock = { result in
    switch result {
    case .success:
        self.syncQueue.async {
            self.setSubscription(tablename: recordType, chattext: self.chattext, originatorname: originatorname)
        }
    case .failure(let error):
        print("保存记录时出错：\(error.localizedDescription)")
    }
}
publicDatabase.add(publicSaveOperation)
}
```

##### 设置订阅方法

此方法可以帮助接收者用户感知到对话中有新的聊天内容加入。我们获取对话表名、实际聊天内容以及发起者的名称。然后访问公共数据库，并创建一个 `CKQuerySubscription` 类型的 `subscription` 变量。构造函数需要表名、一个查询构造函数、订阅的名称以及我们想要的选项。在我们的示例代码中，我们设置了当用户为给定表创建或修改新记录时触发的选项。

接下来，我们创建一个通知信息变量，并将其提示文本设置为“你有一条新聊天”。然后将该通知信息添加到 `subscription` 变量中，并在公共数据库上执行保存操作。完成后，我们将线程返回主线程。

```
func setSubscription(tablename: String, chattext:String,originatorname:String){
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
            print("保存订阅时出错：\(error.localizedDescription)")
            self.group.leave()
        } else {
            print("订阅保存成功！")
            self.group.leave()
        }
    }
}
```

##### 检查聊天表是否存在方法

此方法可以帮助用户判断是否与某个其他用户存在聊天对话。它接收接收者和发起者名称作为输入，并有一个完成处理函数，该处理函数返回两个值：一个布尔值，用于指示聊天表是否存在；以及一个错误对象，用于在查询导致系统错误时提供信息。

```
func chatTableExists(receipentname:String, originatorname:String, completion:@escaping (Bool, Error?) -> Void ){
```

记录类型是动态生成的，并定义了一个公共数据库引用变量。然后我们使用记录类型名称创建一个 `CKQuery` 对象，并执行查询。

```
var recordType = "\(originatorname.replacingOccurrences(of: " ", with: ""))_\(receipentname.replacingOccurrences(of: " ", with: ""))"
recordType = recordType.lowercased()
let container = CKContainer.default()
let database = container.publicCloudDatabase
let predicate = NSPredicate(format: "dummy == %@", "1")
let query = CKQuery(recordType: recordType, predicate: predicate)
let operation = CKQueryOperation(query: query)
operation.zoneID = CKRecordZone.default().zoneID
```

`queryResultBlock` 会异步等待，成功时，完成处理函数返回 `true`，否则返回 `false`。

```
operation.queryResultBlock = { result in
    DispatchQueue.main.async {
        switch result {
        case .failure(let error):
            print(error)
            debugPrint(error)
            // 处理查询结果失败
            //self.syncQueue.async {
            completion(false, error)
            //  self.group.leave()
            //}
        case .success(_):
            // 处理查询结果成功
            //self.syncQueue.async {
            completion(true, nil)
            //  self.group.leave()
            //}
        }
    }
}
database.add(operation)
}
```

##### 获取最后一条聊天方法

此方法会从对话记录中获取最后一条聊天内容。它接收聊天表名作为输入，并有一个包含两个变量的完成处理函数：一个对话数组对象和一个错误对象。

在方法级别定义了一个名为 `chats` 的对话数组。创建一个 `Query` 对象来检索所有记录，并根据创建日期按逆序（从新到旧）对结果进行排序。查询配置对象设置为 10 秒，并将操作添加到公共数据库中。

```
func getlastChat(chattablename:String, completion:@escaping ([Conversation], Error?) -> Void){
    var chats:[Conversation] = []
    let pred = NSPredicate(format: "dummy == %@", "1")
    let query = CKQuery(recordType: "\(chattablename)", predicate: pred)
    query.sortDescriptors = [NSSortDescriptor(key: "datecreated", ascending: false)]
    let operation = CKQueryOperation(query: query)
    operation.resultsLimit = 1
    CKContainer.default().publicCloudDatabase.add(operation)
    let operationConfiguration = CKOperation.Configuration()
    operationConfiguration.timeoutIntervalForRequest = 10
    operationConfiguration.timeoutIntervalForResource = 10
    operation.configuration = operationConfiguration
```

在 `recordMatchedBlock` 中，如果成功，它会遍历所有对话并将其添加到 `chats` 数组对象中。如果查询失败，它会在控制台打印错误信息。

```
operation.recordMatchedBlock = { recordID, result in
    switch result {
    case .success(let record):
        let chat = Conversation(chattext: record["chattext"] as? String, originatorid: record["originatorid"] as? String)
        chat.record = record
        chats.append(chat)
    case .failure(let error):
        print(error.localizedDescription)
    }
}
```

最后，在 `queryResultBlock` 中，我们完成处理函数，要么返回对话（成功时），要么返回错误（失败时）。

```
operation.queryResultBlock = { result in
    DispatchQueue.main.async {
        switch result {
        case .failure(let error):
            debugPrint(error)
            print(error)
            completion(nil, error)
        case .success(_):
            completion(chats, nil)
        }
    }
}
}
}
```

### 完整代码

以下是对话模态类的完整代码：


```swift
//
//  Conversation.swift
//  Chat
//
//  Created by Shantanu Baruah on 2024/6/23.
//

import UIKit
import CloudKit

class Conversation: NSObject {
    let group = DispatchGroup()
    let syncQueue = DispatchQueue(label: "com.domain.app.sections")
    var record: CKRecord!
    var chattext: String!
    var originatorid: String!

    init(chattext: String!, originatorid: String!) {
        self.chattext = chattext
        self.originatorid = originatorid
    }

    override init() {
    }

    // MARK: - 查询聊天记录

    // 查询以找到具有正确业务类型的联系人
    func getChats(receipentname: String, originatorname: String, completion: @escaping ([Conversation], Error?) -> Void) {
        var chats: [Conversation] = []
        var recordType = "\(originatorname.replacingOccurrences(of: " ", with: ""))_\(receipentname.replacingOccurrences(of: " ", with: ""))"
        recordType = recordType.lowercased()
        let pred = NSPredicate(format: "dummy == %@", "1")
        let query = CKQuery(recordType: "\(recordType)", predicate: pred)
        query.sortDescriptors = [NSSortDescriptor(key: "datecreated", ascending: true)]
        let operation = CKQueryOperation(query: query)

        CKContainer.default().publicCloudDatabase.add(operation)

        let operationConfiguration = CKOperation.Configuration()
        operationConfiguration.timeoutIntervalForRequest = 10
        operationConfiguration.timeoutIntervalForResource = 10
        operation.configuration = operationConfiguration
        operation.resultsLimit = 2000

        operation.recordMatchedBlock = { recordID, result in
            switch result {
            case .success(let record):
                let chat = Conversation(chattext: record["chattext"] as? String, originatorid: record["originatorid"] as? String)
                chat.record = record
                chats.append(chat)
            case .failure(let error):
                print(error.localizedDescription)
            }
        }

        operation.queryResultBlock = { result in
            DispatchQueue.main.async {
                switch result {
                case .failure(let error):
                    debugPrint(error)
                    print(error)
                    completion(chats, error)
                case .success(_):
                    completion(chats, nil)
                }
            }
        }
    }

    func saveNewChatRecordsToCloud(receipentname: String, originatorname: String, receipentprofile: CKAsset, originatorprofile: CKAsset, lastchat: String) {
        let container = CKContainer.default()
        let publicDatabase = container.publicCloudDatabase
        let privateDatabase = container.privateCloudDatabase

        var recordType = "\(originatorname.replacingOccurrences(of: " ", with: ""))_\(receipentname.replacingOccurrences(of: " ", with: ""))"
        recordType = recordType.lowercased()

        let businessChatRecord = CKRecord(recordType: "\(receipentname.replacingOccurrences(of: " ", with: "").lowercased())")
        businessChatRecord["chattablename"] = "\(recordType)"
        businessChatRecord["originatorname"] = originatorname
        businessChatRecord["originatorprofile"] = CKAsset(fileURL: originatorprofile.fileURL!)
        businessChatRecord["dummy"] = "1"
        businessChatRecord["lastchat"] = lastchat

        let chatRecord = CKRecord(recordType: "\(recordType)")
        chatRecord["originatorid"] = self.originatorid
        chatRecord["chattext"] = self.chattext
        chatRecord["datecreated"] = Date()
        chatRecord["dummy"] = "1"

        let personalChatRecord = CKRecord(recordType: "chattables")
        personalChatRecord["chattablename"] = "\(recordType)"
        personalChatRecord["receipentprofile"] = CKAsset(fileURL: receipentprofile.fileURL!)
        personalChatRecord["receipentname"] = receipentname
        personalChatRecord["dummy"] = "1"
        personalChatRecord["lastchat"] = lastchat

        let publicSaveOperation = CKModifyRecordsOperation(recordsToSave: [businessChatRecord, chatRecord])
        publicSaveOperation.savePolicy = .changedKeys
        publicSaveOperation.modifyRecordsResultBlock = { result in
            switch result {
            case .success:
                self.syncQueue.async {
                    let privateSaveOperation = CKModifyRecordsOperation(recordsToSave: [personalChatRecord])
                    privateSaveOperation.savePolicy = .changedKeys
                    privateSaveOperation.modifyRecordsResultBlock = { result in
                        switch result {
                        case .success:
                            self.syncQueue.async {
                                self.group.leave()
                            }
                        case .failure(let error):
                            print("保存记录到私有数据库时出错: \(error.localizedDescription)")
                        }
                    }
                    privateDatabase.add(privateSaveOperation)
                }
            case .failure(let error):
                print("保存记录时出错: \(error.localizedDescription)")
            }
        }
        publicDatabase.add(publicSaveOperation)
    }

    // MARK: - 保存聊天记录

    func saveNewChatToCloud(tablename: String, receipentname: String, originatorname: String) {
        let container = CKContainer.default()
        let publicDatabase = container.publicCloudDatabase

        var recordType = "\(originatorname.replacingOccurrences(of: " ", with: ""))_\(receipentname.replacingOccurrences(of: " ", with: ""))"
        recordType = recordType.lowercased()

        let chatRecord = CKRecord(recordType: "\(recordType)")
        chatRecord["originatorid"] = self.originatorid
        chatRecord["chattext"] = self.chattext
        chatRecord["datecreated"] = Date()
        chatRecord["dummy"] = "1"

        let publicSaveOperation = CKModifyRecordsOperation(recordsToSave: [chatRecord])
        publicSaveOperation.savePolicy = .changedKeys
        publicSaveOperation.modifyRecordsResultBlock = { result in
            switch result {
            case .success:
                self.syncQueue.async {
                    self.setSubscription(tablename: recordType, chattext: self.chattext, originatorname: originatorname)
                }
            case .failure(let error):
                print("保存记录时出错: \(error.localizedDescription)")
            }
        }
        publicDatabase.add(publicSaveOperation)
    }

    // MARK: - 通知

    func setSubscription(tablename: String, chattext: String, originatorname: String) {
        let container = CKContainer.default()
        let publicDatabase = container.publicCloudDatabase

        let subscription = CKQuerySubscription(
            recordType: "\(tablename)",
            predicate: NSPredicate(value: true),
            subscriptionID: "mySubscription",
            options: [.firesOnRecordCreation, .firesOnRecordUpdate]
        )

        let notificationInfo = CKSubscription.NotificationInfo()
        print(chattext)
        notificationInfo.alertBody = "您有一条新聊天消息"
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

    // MARK: - 检查请求方与发送方之间的聊天表是否存在。如果存在，则通知调用方

    func chatTableExists(receipentname: String, originatorname: String, completion: @escaping (Bool, Error?) -> Void) {
        var recordType = "\(originatorname.replacingOccurrences(of: " ", with: ""))_\(receipentname.replacingOccurrences(of: " ", with: ""))"
        recordType = recordType.lowercased()

        let container = CKContainer.default()
        let database = container.publicCloudDatabase

        let predicate = NSPredicate(format: "dummy == %@", "1")
        let query = CKQuery(recordType: recordType, predicate: predicate)
        let operation = CKQueryOperation(query: query)
        operation.zoneID = CKRecordZone.default().zoneID

        operation.queryResultBlock = { result in
            DispatchQueue.main.async {
                switch result {
                case .failure(let error):
                    print(error)
                    debugPrint(error)
                    completion(false, error)
                case .success(_):
                    completion(true, nil)
                }
            }
        }
        database.add(operation)
    }

    func getlastChat(chattablename: String, completion: @escaping ([Conversation], Error?) -> Void) {
        var chats: [Conversation] = []
        let pred = NSPredicate(format: "dummy == %@", "1")
        let query = CKQuery(recordType: "\(chattablename)", predicate: pred)
        query.sortDescriptors = [NSSortDescriptor(key: "datecreated", ascending: false)]
        let operation = CKQueryOperation(query: query)
        operation.resultsLimit = 1

        CKContainer.default().publicCloudDatabase.add(operation)

        let operationConfiguration = CKOperation.Configuration()
        operationConfiguration.timeoutIntervalForRequest = 10
        operationConfiguration.timeoutIntervalForResource = 10
        operation.configuration = operationConfiguration

        operation.recordMatchedBlock = { recordID, result in
            switch result {
            case .success(let record):
                let chat = Conversation(chattext: record["chattext"] as? String, originatorid: record["originatorid"] as? String)
                chat.record = record
                chats.append(chat)
            case .failure(let error):
                print(error.localizedDescription)
            }
        }

        operation.queryResultBlock = { result in
            DispatchQueue.main.async {
                switch result {
                case .failure(let error):
                    debugPrint(error)
                    print(error)
                    completion(nil, error)
                case .success(_):
                    completion(chats, nil)
                }
            }
        }
    }
}
```


