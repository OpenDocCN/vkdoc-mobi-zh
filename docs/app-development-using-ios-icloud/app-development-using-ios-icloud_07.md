# 2. 块方法

您的由 iCloud 数据库驱动的 iPhone 应用需要查询数据库，主要目的是在应用屏幕上显示结果。iCloud 数据库有一个 `CKQueryOperation` 对象，可用于执行查询。要执行查询，您需要执行以下操作：

*   使用所需的搜索条件创建一个 `CKQuery` 对象。您还可以在设置查询和表所在的区域时设置数据排序选项。请注意，CloudKit 将查询限制在特定记录区域内。每个 `CKQueryOperation` 仅限于单个区域。

*   创建一个 `CKQueryOperation` 对象，并使用之前定义的 `CKQuery` 对象进行初始化。

*   （可选）我们还可以配置参数，例如 `resultsLimit`（这将限制触发查询时要获取的记录数）和 `desiredKeys`（您可以指定希望查询返回的查询表的字段）。

*   最后，我们可以将 `CKQueryOperation` 对象传递给目标数据库（公共、私有或共享）以执行操作。

当为一个查询分配了完成块处理程序时，`CKQueryOperation` 会在查询执行完毕并返回任何结果后调用该处理程序。该处理程序可用于处理错误、失败和结果。

在 iOS 15 中，我们使用了 `recordFetchedBlock` 和 `queryCompletionBlock`。虽然 `recordFetchedBlock` 已被弃用，但 `queryCompletionBlock` 仍然可用。然而，在本章中，我们将重点介绍名为 `recordMatchedBlock` 和 `queryResultBlock` 的完成块处理程序。下面是一个使用 `recordFetchedBlock` 和 `queryCompletionBlock` 的 `CKQuery` 示例。

## 示例代码说明

在此示例代码中，我们查询当前用户的私有云数据库，以检索 iCloud 表 `chattables` 中的所有记录。代码完成后，一个类对象 `Chattable`（代表 iCloud 表 `chattables`）会被填充并返回给调用函数。让我们来理解这段代码。

### 函数定义

```
func gettableNames(completion:@escaping ([Chattable], Error?) -> Void){
```

函数名为 `getTableNames`，其参数之一是一个特殊的关键字 `completion@escaping`。这个关键字意味着当函数执行完成时，闭包会被调用，在我们的示例中，该闭包传递了一个类对象 `Chattable` 的实例。稍后您将看到我们如何以及何时调用 `completion`。

当函数是异步操作时，`@escaping` 是一个重要的标记。异步操作可能需要一段时间才能被触发。它还让 Swift 编译器知道，当异步函数完成时，我们正在以一种合乎逻辑的方式退出该函数。在下面的示例中，我们调用了 iCloud 查询函数，这是一个异步函数，因此 `completion@escaping` 非常适合实现我们的函数目标。

### 返回值描述

```
var chattables:[Chattable] = []
```

声明一个类类型为 `Chattable` 的对象数组 `chattables`。这个变量将保存查询执行后返回的所有表名，并通过 `completion@escaping` 返回给调用函数。

### 查询定义

```
let pred = NSPredicate(format: "dummy == %@", "1")
```

使用 `NSPredicate` 定义查询谓词，并存储在变量 `pred` 中。条件设置为返回表字段 `dummy` 值为 1 的所有记录。

```
let query = CKQuery(recordType: "chattables", predicate: pred)
```

上面定义了一个名为 `query` 的对象，类型为 `CKQuery`。这里为 iCloud 对象类型 `chattables` 定义了查询，并且通过之前定义的 `pred` 变量设置了检索条件。`CKQuery` 有两个输入：记录类型和谓词。

```
let operation = CKQueryOperation(query: query)
```

接下来，我们定义 `CKQueryOperation` 并将查询对象分配给它。

```
let operationConfiguration = CKOperation.Configuration()
```

定义 `CKOperation.Configuration` 是可选的。我们定义它是为了设置超时间隔，以便查询在特定时间后无响应时可以退出。

```
operationConfiguration.timeoutIntervalForRequest = 10
```

此属性确定查询完成的请求超时间隔（以秒为单位）。在我们的示例中，任务将在放弃之前等待 10 秒以等待额外的数据到达。

```
operation.configuration = operationConfiguration
```

我们创建的操作配置对象现在被分配给我们之前定义的 `operation` 对象。

```
CKContainer.default().privateCloudDatabase.add(operation)
```

该操作现在被分配给用户私有数据库以供执行。



### 查询匹配块

```
operation.recordMatchedBlock = { recordID, result in }
```

iCloud 数据库调用是异步的。查询执行后，结果会异步返回。`recordMatchedBlock` 是操作返回值时执行的代码块。`recordMatchedBlock` 包含两个参数，如下所述：

*   `recordID`，类型为 `CKRecord.ID`
*   `result`，这是一个枚举类型，为 `Result<CKRecord, Error>`

该代码块会遍历所有返回的记录。

```
switch result {
```

现在我们需要解包 `result` 枚举，对于每条记录，它包含两个数据元素——`success` 和 `failure`。上述语句将帮助我们在两个数据元素之间进行切换。

```
case .success(let record):
let chattable = Chattable(chattablename: record["chattablename"] as? String, receipentprofile: record["receipentprofile"] as? CKAsset, receipentname: record["receipentname"] as? String, lastchat: record["lastchat"] as? String)
chattable.record = record
chattables.append(chattable)
```

这是第一种情况——`success`，意味着有一条记录被返回。我们将返回的结果捕获到变量 `record` 中。随后，我们将提取记录的必填字段，存储到 `Chattable` 类的作用域级别局部变量中，然后将该变量追加到函数级别的 `Chattable` 数组对象中。由于这是迭代性质的，所有记录都会被追加到我们的函数级变量 `chattables` 中。

```
case .failure(let error):
print(error.localizedDescription)
}
```

这是另一种情况——`failure`。如果记录无法检索，我们将在控制台上打印 `error` 对象的本地化描述。

### 查询结果块

```
operation.queryResultBlock = { result in
```

当 `recordMatchedBlock` 遍历完所有记录后，下一个被调用的块是 `queryResultBlock`。它有一个参数 result，是一个枚举类型，为 `Result<CKQueryOperation.Cursor?, Error>`。

`CKQueryOperation.Cursor` 包含记录和错误对象（如果存在错误）。

```
DispatchQueue.main.async {
```

我们定义了一个类级别的 `DispatchQueue` 对象，它将等待主线程上的异步作业完成。完成后，将执行 `DispatchQueue` 的主体。

```
switch result {
```

我们正在展开 `result` 枚举，处理其两个参数 `failure` 和 `success`。

```
case .failure(let error):
debugPrint(error)
print(error)
completion(chattables, error)
```

如果出现失败，则执行此代码块，我们将在屏幕上打印错误信息。请注意，我们现在调用完成处理程序，传入 `chattables` 数组对象和报告的错误，以便调用函数采取适当的操作。

```
case .success(_):
completion(chattables, nil)
}
```

这是成功块。如果成功，则调用完成处理程序，传入 `chattables` 对象，错误标记为 nil。

### 完整代码

```
func gettableNames(completion:@escaping ([Chattable], Error?) -> Void){
var chattables:[Chattable] = []
let pred = NSPredicate(format: "dummy == %@", "1")
let query = CKQuery(recordType: "chattables", predicate: pred)
let operation = CKQueryOperation(query: query)
let operationConfiguration = CKOperation.Configuration()
operationConfiguration.timeoutIntervalForRequest = 10
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
completion(chattables, error)
case .success(_):
completion(chattables, nil)
}
}
}
}
```

