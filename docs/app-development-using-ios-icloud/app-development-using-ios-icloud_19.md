# Businesstable 模态类

该模态类代表每个用户的业务表。请注意，每个用户都会有一个名称与其业务名称相同的表。它是一个供所有已发起聊天的用户使用的参考表。我们还将发起人（originator）的个人资料信息存储在该表中，尽管它是对存储在个人资料中信息的重复，但有助于快速检索发起人信息，而无需浪费昂贵的查询资源。

在下面的代码中，我们将 `Businesstable` 类定义为 `NSObject` 的子类，它继承了 `UIKit` 和 `CloudKit` 两个模块。定义了队列和组对象，它们将有助于调用 iCloud 异步 CRUD 函数。该类将表的以下字段模拟为变量：用户与之发起对话的聊天表名称、用户的姓名和头像图片以及最后一条聊天文本。定义了一个 `init` 方法来初始化这些变量。同时定义了一个空的 `init` 方法，以防我们需要在不初始化变量的情况下使用类函数。

```
import UIKit
import CloudKit
class Businesstable: NSObject {
let group = DispatchGroup()
let syncQueue = DispatchQueue(label: "com.domain.app.sections")
var chattablename:String!
var originatorprofile:CKAsset!
var originatorname:String!
var lastchat:String!
var record:CKRecord!
init(chattablename: String!, originatorprofile: CKAsset!, originatorname: String!, lastchat: String!) {
self.chattablename = chattablename
self.originatorprofile = originatorprofile
self.originatorname = originatorname
self.lastchat = lastchat
}
override init(){
}
```



#### 获取表名方法

该方法用于获取用户的所有聊天表名称。该方法接收一个表名（用户的业务名称）作为查询目标，并通过一个完成处理程序返回一个包含所有表名的数组。

定义了一个方法级数组变量 `businesstables`，用于存储所有业务表名称。在一个虚拟变量上构建查询对象（每条记录的值都存储为 1，因此将返回所有记录），并将该查询添加到将在用户公共数据库上执行的操作对象中。设置了一个 10 秒超时的操作配置，并将其添加到操作配置设置中。

```
func gettableNames(tablename: String, completion:@escaping ([Businesstable], Error?) ->Void ){
var businesstables:[Businesstable] = []
let pred = NSPredicate(format: "dummy == %@", "1")
let query = CKQuery(recordType: "\(tablename)", predicate: pred)
let operation = CKQueryOperation(query: query)
CKContainer.default().publicCloudDatabase.add(operation)
let operationConfiguration = CKOperation.Configuration()
operationConfiguration.timeoutIntervalForRequest = 10
operationConfiguration.timeoutIntervalForResource = 10
operation.configuration = operationConfiguration
```

如果执行成功，`recordMatchedBlock` 将遍历所有记录并将其存储到方法级别的 `businesstables` 数组中。如果执行失败，则会在控制台输出错误信息。

```
operation.recordMatchedBlock = { recordID, result in
switch result {
case .success(let record):
let businesstable = Businesstable(chattablename: record["chattablename"] as? String, originatorprofile: record["originatorprofile"] as? CKAsset, originatorname: record["originatorname"] as? String, lastchat: record["lastchat"] as? String)
businesstable.record = record
businesstables.append(businesstable)
case .failure(let error):
print(error.localizedDescription)
}
}
```

`queryResultBlock` 将对 `enum` 结果进行判断，如果成功则通过完成处理程序返回 `businesstables` 数组，如果失败则返回错误对象。

```
operation.queryResultBlock = { result in
DispatchQueue.main.async {
switch result {
case .failure(let error):
debugPrint(error)
print(error)
completion(businesstables, error)
case .success(_):
completion(businesstables, nil)
}
}
}
}
```

### 完整代码

以下是 `Businesstable` 类的完整代码：

```
//
//  Businesstable.swift
//  Chat
//
//  Created by Shantanu Baruah on 6/25/24.
//
import UIKit
import CloudKit
class Businesstable: NSObject {
let group = DispatchGroup()
let syncQueue = DispatchQueue(label: "com.domain.app.sections")
var chattablename:String!
var originatorprofile:CKAsset!
var originatorname:String!
var lastchat:String!
var record:CKRecord!
init(chattablename: String!, originatorprofile: CKAsset!, originatorname: String!, lastchat: String!) {
self.chattablename = chattablename
self.originatorprofile = originatorprofile
self.originatorname = originatorname
self.lastchat = lastchat
}
override init(){
}
func gettableNames(tablename: String, completion:@escaping ([Businesstable], Error?) ->Void ){
var businesstables:[Businesstable] = []
let pred = NSPredicate(format: "dummy == %@", "1")
print(tablename)
let query = CKQuery(recordType: "\(tablename)", predicate: pred)
let operation = CKQueryOperation(query: query)
CKContainer.default().publicCloudDatabase.add(operation)
let operationConfiguration = CKOperation.Configuration()
operationConfiguration.timeoutIntervalForRequest = 10
operationConfiguration.timeoutIntervalForResource = 10
operation.configuration = operationConfiguration
operation.recordMatchedBlock = { recordID, result in
switch result {
case .success(let record):
let businesstable = Businesstable(chattablename: record["chattablename"] as? String, originatorprofile: record["originatorprofile"] as? CKAsset, originatorname: record["originatorname"] as? String, lastchat: record["lastchat"] as? String)
businesstable.record = record
businesstables.append(businesstable)
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
completion(businesstables, error)
case .success(_):
completion(businesstables, nil)
}
}
}
}
}
```

