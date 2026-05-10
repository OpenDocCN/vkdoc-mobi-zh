# Chattable 模态类

该类模拟了 `chattables` iCloud 仓库。以下代码将 `Chattable` 类定义为 `NSObject` 的子类；它继承了 `UIKit` 和 `CloudKit` 模块。该类还定义了用于异步 iCloud 方法管理的组（group）和队列（queue）。

该类将聊天表名称、接收者头像和姓名以及聊天文本作为其变量。定义一个 `init` 方法，用于使用用户提供的值来初始化该类的对象。同时定义了一个空的 `init` 方法，以便在不初始化变量的情况下调用该类（通常这样做是为了访问方法）。

```
import UIKit
import CloudKit
class Chattable: NSObject {
let group = DispatchGroup()
let syncQueue = DispatchQueue(label: "com.domain.app.sections")
var chattablename:String!
var receipentprofile:CKAsset!
var receipentname:String!
var lastchat:String!
var record:CKRecord!
init(chattablename: String!, receipentprofile: CKAsset!, receipentname: String!, lastchat: String!) {
self.chattablename = chattablename
self.receipentprofile = receipentprofile
self.receipentname = receipentname
self.lastchat = lastchat
}
override init(){
}
```

#### 获取表名方法

此方法获取当前用户的所有表名，并通过完成处理程序（completion handler）返回。

该类的数组变量定义为 `chattables`。为 `chattables` iCloud 仓库创建一个 `CKQuery` 对象，并将其添加到操作（operation）中，该操作在公共数据库上执行。

```
func gettableNames(completion:@escaping ([Chattable], Error?) -> Void){
var chattables:[Chattable] = []
let pred = NSPredicate(format: "dummy == %@", "1")
let query = CKQuery(recordType: "chattables", predicate: pred)
let operation = CKQueryOperation(query: query)
let operationConfiguration = CKOperation.Configuration()
operationConfiguration.timeoutIntervalForRequest = 10
operationConfiguration.timeoutIntervalForResource = 10
operation.configuration = operationConfiguration
CKContainer.default().privateCloudDatabase.add(operation)
```

在 `recordMatchedBlock` 中，我们遍历并切换 `enum` 结果对象；成功时，将找到的 chattable 追加到 `chattables` 数组对象中；失败时，在控制台打印错误。

```
operation.recordMatchedBlock = { recordID, result in
switch result {
case .success(let record):
let chattable = Chattable(chattablename: record["chattablename"] as? String, receipentprofile: record["receipentprofile"] as? CKAsset, receipentname: record["receipentname"] as? String, lastchat: record["lastchat"] as? String)
chattable.record = record
chattables.append(chattable)
case .failure(let error):
print(error.localizedDescription)
}
}
```

在 `queryResultBlock` 中，我们完成完成处理程序。成功时，处理程序返回 `chattables`；失败时，则将错误返回给调用方法。

```
operation.queryResultBlock = { result in
DispatchQueue.main.async {
switch result {
case .failure(let error):
debugPrint(error)
print(error)
completion(nil, error)
case .success(_):
completion(chattables, nil)
}
}
}
}
}
```

### 完整代码

以下是 Chattable 模态类的完整代码：

```
//
//  Chattable.swift
//  Chat
//
//  Created by Shantanu Baruah on 6/25/24.
//
import UIKit
import CloudKit
class Chattable: NSObject {
let group = DispatchGroup()
let syncQueue = DispatchQueue(label: "com.domain.app.sections")
var chattablename:String!
var receipentprofile:CKAsset!
var receipentname:String!
var lastchat:String!
var record:CKRecord!
init(chattablename: String!, receipentprofile: CKAsset!, receipentname: String!, lastchat: String!) {
self.chattablename = chattablename
self.receipentprofile = receipentprofile
self.receipentname = receipentname
self.lastchat = lastchat
}
override init(){
}
func gettableNames(completion:@escaping ([Chattable], Error?) -> Void){
var chattables:[Chattable] = []
let pred = NSPredicate(format: "dummy == %@", "1")
let query = CKQuery(recordType: "chattables", predicate: pred)
let operation = CKQueryOperation(query: query)
let operationConfiguration = CKOperation.Configuration()
operationConfiguration.timeoutIntervalForRequest = 10
operationConfiguration.timeoutIntervalForResource = 10
operation.configuration = operationConfiguration
CKContainer.default().privateCloudDatabase.add(operation)
operation.recordMatchedBlock = { recordID, result in
switch result {
case .success(let record):
let chattable = Chattable(chattablename: record["chattablename"] as? String, receipentprofile: record["receipentprofile"] as? CKAsset, receipentname: record["receipentname"] as? String, lastchat: record["lastchat"] as? String)
chattable.record = record
chattables.append(chattable)
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
completion(chattables, nil)
}
}
}
}
}
```

