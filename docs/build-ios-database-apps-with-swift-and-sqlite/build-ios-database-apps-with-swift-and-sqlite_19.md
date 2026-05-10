# 注意  
对于其他数据类型，将 Swift 数据绑定到 SQLite 列的过程与我在上一段中概述的几乎相同。请查阅 SQLite Prepared Statement 网页（https://www.sqlite.org/c3ref/stmt.html），了解完整的方法和函数列表以及相应的参数要求和返回类型。  

拿到结果后，我只需要将值赋给 `Db` 对象的 `name` 属性，并将该值追加到结果集数组中。  

该数组被返回给 `MasterViewController` 中的调用数组变量，该变量将更新 `TableView` 的数据源，并将数据重新加载到 `UITableView` 中。  

至此，关于为 `TableView` 填充数据源的讨论就结束了。我演示了如何从 SQLite 数据库中检索值。正如我之前提到的，我们将在后面的专门章节中探讨 `SELECT` 语句及其 API。  

```
func populateIndexView(_ sender:AnyObject, query:String)->Array<String>{
    var resultset = [String]()
    let sourceVC:DetailViewController = nil;
    if( sender.sourceViewController.isKindOfClass(DetailViewController)){
        sourceVC:DetailViewController = sender as! DetailViewController
    }
    if sqlite3_open(dbPath.path!, &db) != SQLITE_OK {
        errmsg = "error opening database"
        print(errmsg)
        sourceVC.detailSQLiteQueryField.text = errmsg
    }
    let statuscode = sqlite3_prepare_v2(db, query.cString(using: String.Encoding.utf8)!,
                                        -1, &sqlStatement, nil)
    if(statuscode != Enums.SQLiteStatusCode.ok.rawValue){
        errmsg = String (cString: sqlite3_errmsg(db))!
        print("error preparing select: \(errmsg)")
        if( sender.sourceViewController.isKind(of: DetailViewController.self)){
            let sourceVC:DetailViewController = sender as! DetailViewController
            sourceVC.detailSQLiteQueryField.text = errmsg
        }
    }else{
        while (sqlite3_step(sqlStatement)==Enums.SQLiteStatusCode.row.rawValue){
            let dbVal:Db=Db.init()
            dbVal.name = String (cString: UnsafePointer<Int8>(sqlite3_column_text(sqlStatement, 1)))!
            resultset.append(dbVal.name)
        }
        sqlite3_finalize(sqlStatement);
    }
    return resultset
}
```

## `initViewIndex` 函数  

接下来我们要看的 Swift 函数是 `initViewIndex`。这个函数只有一个职责：遍历 Documents 目录并获取 SQLite 数据库列表。该列表被插入到一个名为 `sqliteDbs` 的数组中，并返回给调用对象。当应用从 `MasterViewController` 的 `viewDidLoad` 函数加载时，会调用此函数。  

```
func initViewIndex()->Array<String>{
    var sqliteDbs:[String]=[]
    let documentsDir = FileManager.default.urlsForDirectory(.documentDirectory,
                                                           inDomains: .userDomainMask).first!
    do {
        let directoryUrls = try FileManager.default.contentsOfDirectory(at:
            documentsDir, includingPropertiesForKeys: nil, options: FileManager.DirectoryEnumerationOptions())
        sqliteDbs = directoryUrls.filter{$0.pathExtension! == "sqlite" }.map{
            $0.lastPathComponent! }
    } catch let error as NSError {
        print(error.localizedDescription)
    }
    return sqliteDbs
}
```

该函数中的逻辑非常简单。它首先通过 `NSFileManager.default` 类和函数获取 Documents 目录的句柄。然后，它尝试使用 `NSFileManager` 类中的 `contentsOfDirectoryAtURL` 方法检索 Documents 目录（包括任何子目录）中的所有内容。  

对列表应用了一个过滤器，以便只返回扩展名为 `.sqlite` 的文件（这里我假设所有 SQLite 数据库都有 `.sqlite` 扩展名，但在生产环境中可能并非如此）。使用 `map` 仅提取返回路径的最后一部分。  

`filter` 和 `map` 设计在最新版本的 Swift 中已重新设计，以应用某种 mapReduce 模式。这种设计模式消除了遍历数组的需要，因此效率更高。`filter` 使用布尔表达式来测试并返回缩减后的列表。`map` 函数用于提取或转换已被过滤的集合的某些部分。  

## `executeQuery` 函数  

顾名思义，`executeQuery` 执行 SQLite 查询。我在与 `DetailedViewController` 中的“执行查询”按钮配合使用时大量使用了此函数，我们稍后将对此进行探讨。该函数的执行路径与执行 SQLite 操作的其他函数类似——即确保数据库已打开，或根据需要结合使用 `sqlite3_open` 命令将其打开。如果无法打开（或创建）数据库，它会返回一条错误消息，该消息将显示在 `DetailViewController` 的编辑器字段中。  

```
func executeQuery(_ sender:AnyObject, query:String?)->String{
    let sourceVC:DetailViewController = nil;
    if( sender.sourceViewController.isKindOfClass(DetailViewController)){
        sourceVC = sender as! DetailViewController
    }
    var statuscode:integer_t=0
    //There is no error checking but you should have it in a production app
    //You should see if database is present and open also.
    if sqlite3_open(dbPath.path!, &db) != Enums.SQLiteStatusCode.ok.rawValue {
        errmsg = "error opening database or database does not exist"
    }else{
        let query = query!.replacingOccurrences(of: "\n", with: "")
        statuscode = sqlite3_exec(db, query.cString(using: String.Encoding.utf8)!, nil,
                                 nil, dbErr);
        if(statuscode != Enums.SQLiteStatusCode.ok.rawValue){
            errmsg = String.fromCString(sqlite3_errmsg(db))!
        }else{
            errmsg = "query was successful"
        }
    }
    return errmsg
}
```

接下来，该函数通过 `stringByReplacingOccurencesOfString` 去除换行符 `\n` 来准备查询字符串，因为，如果你还记得，我之前定义的模板包含了换行符，我希望它们在 SQL 编辑器中能够正确显示（稍后会展示）。当然，如果查询字符串不包含任何这些字符，那么这行代码就不会执行任何操作。  

重要的一行代码是 `sqlite3_exec` 命令。这个命令非常棒，因为它封装了 `sqlite3_prepare_v2`、`sqlite3_step` 和 `sqlite3_finalize`。我在执行插入、更新或删除操作时倾向于使用此命令，而对于选择查询则选择使用较长的版本，仅仅是因为我觉得这样可以更好地将返回的列映射到自定义数据类型。将 `sqlite3_exec` 与 `SELECT` 查询一起使用是可行的；但是，你需要提供一个回调函数，以便第三个参数能够处理结果集。该命令返回一个状态码，然后我使用该状态码来更新编辑器，以反映事务的状态。  

## `openSQLiteDatabase` 函数  

`openSQliteDatabase` 函数是一个简单的函数，与其他一些函数类似。它唯一的职责是创建数据库，并使用来自 `DetailViewController` 中编辑器（`UITextArea`）字段的用户提供的文件名将其打开。正如我们之前所见，SQLite 数据库是在 Documents 目录中创建的，因为这是应用程序沙盒中少数几个具有读写权限的位置之一。如果你在应用的根目录下创建文件，它将自动变为只读，更糟糕的是，你或你的应用用户不会收到任何关于它不可写的错误消息。  

```
func openSQLiteDatabase(databaseName:String)->Enums.SQLiteStatusCode{
    //SQLite database is always created in Documents directory
    // I am assuming that the database name has the .sqlite extension for the sake of simplicity
    //Also, you would need to check if databaseName is not empty
    let dirManager = FileManager.default()
    do {
        let directoryURL = try dirManager.urlForDirectory(FileManager.
```



`SearchPathDirectory.documentDirectory`，在 `FileManager.SearchPathDomainMask.userDomainMask` 中，适用于 `nil`，创建 `true`）

`dbPath = try! directoryURL.appendingPathComponent(databaseName)`

`} catch let err as NSError {`

`print("Error: \(err.domain)")`

`}`

`return Enums.SQLiteStatusCode(rawValue: sqlite3_open(dbPath.absoluteString.cString(using: String.Encoding.utf8)!, &db))!`

该函数获取文档目录的句柄，并使用 `URLByAppendingPathComponent` 将数据库文件的名称附加到路径中。然后使用 `sqlite3_open` 命令创建数据库、打开它并返回状态码。请注意，必须使用 `absoluteString.cStringUsingEncoding` 和 `UTF8` 编码将路径转换为 SQLite 能理解的 `char` 数据类型。

这样就完成了 `DbMgrDAO` 类的逻辑。接下来的两节将介绍如何设置 `MasterViewController`、`DetailViewController`，最后是用户界面。

## MasterViewController

`MasterViewController` 使用了一个 `UITableView` 组件。`TableView` 在多区段视图中同时显示数据库及其对应的模式。当数据库被创建时，它们会被添加到 `TableView` 的数据源中，并重新加载显示。当选中一个数据库文件时，系统会使用 `SELECT` 查询从数据库中检索相应的模式，并将其显示在与表、视图、索引或触发器模式元素关联的区段中。

需要为 `UITableView`、其数据源及其委托实现多个函数，以及几个自定义函数。

```
import UIKit

class MasterViewController: UITableViewController {

    var detailViewController: DetailViewController? = nil
    var objects = [AnyObject]()

    // 不可变数组
    let schemaSections = ["Databases", "Tables", "Views", "Indexes", "Triggers"]
    var schemaDetailsItems=[[String]]()
    var tableArray:[String] = []
    var viewArray:[String] = []
    var indexArray:[String] = []
    var triggerArray:[String] = []
    var dao:DbMgrDAO = DbMgrDAO.init()
```

让我们从自定义属性开始讲起。第一个变量是 `detailViewController`，用于获取 `DetailViewController` 的句柄，这样我就可以将状态信息传回给编辑器，而无需像常规文档路径那样通过 `segue`。是的，该路径是双向的。`objects` 变量是一个通用数组，用于存放所有类型的对象。然后，我设置了 `schemaSection` 这个不可变数组，用于定义数据库及相应模式的表格区段。`schemaDetailsItems` 是一个可变数组，用于存储按表、视图、索引和触发器分类的模式。正如你可能猜到的，`tableArray`、`viewArray`、`indexArray` 和 `triggerArray` 是用于存储各区段具体内容的数组。这几乎就像创建一个 JSON 数据结构，因为最后这四个数组将被附加到 `schemaDetailsItems` 数组中，正如在 `didViewLoad` 函数中看到的那样。这个变量是 `DbMgrDAO` 类的一个实例。我将用它来调用我在上一节中提到的各种函数。

### viewDidLoad 函数实现

```
override func viewDidLoad() {
    super.viewDidLoad()

    schemaDetailsItems.append(dao.initViewIndex())
    schemaDetailsItems.append(tableArray)
    schemaDetailsItems.append(viewArray)
    schemaDetailsItems.append(indexArray)
    schemaDetailsItems.append(triggerArray)

    if let split = self.splitViewController {
        let controllers = split.viewControllers
        self.detailViewController = (controllers[controllers.count-1] as! UINavigationController).topViewController as? DetailViewController
    }
}
```

从上述代码中，你可以推断出如何在 `MasterViewController` 中填充 `UITableView` 数据源的知识。首先，当应用从 `AppDelegate` 对象加载时（该对象在从 `main.swift` 文件启动应用时被调用），会调用 `initViewIndex`。`initViewIndex` 的结果会被附加到 `schemaDetailsItems` 数组中。我还为其他区段附加了空数组；否则，Swift 编译器会报错，提示其他区段为空或不存在。

剩下的代码设置了 `splitViewController`、导航，并将 `detailViewController` 设置为初始或顶层控制器。你无需更改或触碰这些代码。

我提供了一种填充 `TableView` 的方式或技术。不过，你也可以设置一个 `UITableViewCell` 来充当单元格的控制器。在我的案例中，我使用了两个数据源来构建主数据源，因此很难定义一个单元格控制器，但并非不可能实现。

我不会赘述那些我没有使用但作为 `UITableView` 实现一部分的函数，即 `viewWillAppear` 和 `didReceiveMemoryWarning`。

### 数据源函数的实现

以下函数是设置和在 `TableView` 中显示数据所必需的。`titleForHeaderInSection` 返回区段的标题，因此对于此应用，标题分别是 Databases、Tables、Views、Indexes 和 Triggers。`numberOfSectionsinTableView` 函数决定了 `TableView` 将包含多少个区段。对于此应用，该值为 5，即 `schemaSections` 数组中的元素个数。`numberOfRowsInSection` 配置了要显示的行数。这通常是作为数据源的数组中元素的个数。由于我使用了嵌套数组，我将每个区段的行数设置为 `schemaDetailsItems [section].count`。最后一个必需的函数是 `cellForRowAtIndexPath`：

```
override func tableView(_ tableView: UITableView, titleForHeaderInSection section: Int) -> String? {
    return self.schemaSections [section ]
}

override func numberOfSections(in tableView: UITableView) -> Int {
    return self.schemaSections.count
}

override func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
    return schemaDetailsItems [section].count
}

override func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
    let cell = tableView.dequeueReusableCell (withIdentifier: "Cell", for: indexPath)
    cell.textLabel?.text = self.schemaDetailsItems[(indexPath as NSIndexPath).section][(indexPath as NSInpexPath).row]
    return cell
}
```

当需要在场景之间导航时，可以通过定义在不同场景之间连接的线程（称为 segues）来实现。当应用从一个视图（场景）移动到另一个视图时，会调用 `prepareForSeque` 函数。这个函数非常重要，因为它还提供了将数据和对象传输到下一个 `ViewController` 的途径。然而，`prepareForSegue` 只能单向使用。如果你想返回到调用方的 `ViewController`，则需要从场景中展开，这需要创建一个 `unWindFromSegue` 函数。这个概念类似于在互联网浏览器中后退历史路径。不过，使用展开方式时，你一次只能展开一个场景，并且必须按照你前进的顺序进行。



我选择使用`tableView`的`didSelectRowAtIndexPath`方法，该方法返回选中的行。此方法通过代理提供。`selectDb`变量存储指定行索引处的选中值。在确认数据库已打开后，我使用`SELECT`语句查询`sqlite_master`表，并将结果分配给各个可变数组。然后，在移除所有现有项后，我将这些数组追加到`schemaDetailsItems`数组中。如果不这样做，每次点击都会得到一个很长的数组，并且由于该数组与`schemaSections`数组不匹配，项目将无法在`TableView`中显示。一旦数据源设置完毕，我便重新加载`TableView`以刷新视图显示新数据。

```
override func tableView(tableView: UITableView, didSelectRowAt indexPath: IndexPath) {
    var selectdb = schemaDetailsItems[0][(indexPath as NSIndexPath).row]
    
    if(dao.openSQLiteDatabase(selectdb[0]).rawValue == Enums.SQLiteStatusCode.ok.rawValue){
        let db:Db = Db.init()
        let tables:String = db.selectDbSchemaListByType("table") as String
        let views:String = db.selectDbSchemaListByType("view") as String
        let indices:String = db.selectDbSchemaListByType("index") as String
        let triggers:String = db.selectDbSchemaListByType("trigger") as String
        
        tableArray = dao.populateIndexView(self, query:tables)
        viewArray = dao.populateIndexView(self, query:views)
        indexArray = dao.populateIndexView(self, query:indices)
        triggerArray = dao.populateIndexView(self, query:triggers)
    }
    
    schemaDetailsItems.removeAll()
    schemaDetailsItems.append(dao.initViewIndex())
    schemaDetailsItems.append(tableArray)
    schemaDetailsItems.append(viewArray)
    schemaDetailsItems.append(indexArray)
    schemaDetailsItems.append(triggerArray)
    
    tableView.reloadData()
}
```

## 第三章 ■ 运行时创建数据库

### `DetailViewController`

`DetailViewController`包含用于创建和打开 SQLite 数据库、加载 SQL 模板以及执行查询以创建数据库模式的按钮。

以下代码是应用程序中与用户、控制器和模型交互相关的处理逻辑的实现。由于`DetailViewController`是`UIViewController`的子类，它继承了几个由该类提供的方法。

从代码中你会注意到，我创建了`DbMgrDAO`类的一个实例以及`Db`类的一个实例。我调用它们的`init`函数来初始化这些类。

### `createDbButton`的`@IBAction`

`createDbButton`是一个`@IBAction`。正如我们在构建 UI 部分所见，该函数连接到 UI 详情场景中的`UIButton`。我将`detailSQLiteQueryField`的值赋给`dbname`常量。这个值将被传递给之前讨论过的`openSQLiteDatabase`方法，用于创建并打开数据库文件，从而建立与数据库的连接。如果返回值是`OK`或`0`，我将在同一个`detailSQLiteQueryField`中显示成功或确认消息。

```
// DetailViewController.swift
// Db Mgr
//
// Created by Kevin Languedoc on 2016-05-20.
// Copyright © 2016 Kevin Languedoc. All rights reserved.
//

import UIKit

class DetailViewController: UIViewController {
    let dbDAO:DbMgrDAO = DbMgrDAO.init()
    let database:Db = Db.init()
    var dbname:String = ""
    var dbStatusMsg:String = ""
    
    @IBAction func createDbButton(_ sender: AnyObject) {
        dbname = self.detailSQLiteQueryField.text
        if(dbDAO.openSQLiteDatabase(dbname).rawValue == Enums.SQLiteStatusCode.ok.rawValue){
            self.detailSQLiteQueryField.text = "SQLite database \(self.detailSQLiteQueryField.text) is created and open"
            if let delegate = self.delegate {
                delegate.didOpenDatabase(self, databaseName: dbnane)
            }
        }
    }
}
```

这个按钮的代码大致就是这样。接下来我们看看`Detail`场景中连接到其他按钮的剩余`IBActions`函数。

## 第三章 ■ 运行时创建数据库



### 创建数据库模式的 `@IBAction` 按钮

前四个函数非常简单。它们会显示可在 SQLite 数据库中创建的数据库元素（表、视图、触发器和索引）所对应的 SQL 查询语句。第五个函数会调用 `executeQuery` 函数，并将 `detailSQLiteQueryField` 的内容传递给它。返回的消息会赋值给同一个字段。

每个按钮函数都被分配了来自 `database` 对象的 `tableDef`、`viewDef`、`triggerDef` 或 `indexDef` 查询模板，该对象是 `Db` 类的一个实例。

```swift
@IBAction func createTableButton(_ sender: AnyObject) {
    self.detailSQLiteQueryField.text = database.tableDef
}

@IBAction func createViewButton(_ sender: AnyObject) {
    self.detailSQLiteQueryField.text = database.viewDef
}

@IBAction func createTriggerButton(_ sender: AnyObject) {
    self.detailSQLiteQueryField.text = database.triggerDef
}

@IBAction func createIndexButton(_ sender: AnyObject) {
    self.detailSQLiteQueryField.text = database.indexDef
}

@IBAction func executeQueryButton(_ sender: AnyObject) {
    self.detailSQLiteQueryField.text = dbDAO.executeQuery(self.detailSQLiteQueryField.text)
}
```

### `detailSQLiteQueryField` 的 `@IBOutlet`

`detailSQLiteQueryField` 是一个与详情场景中同名的 `UITextArea` 组件关联的 `IBOutlet` 变量。它是 UI 的主要工作区域，用于加载和编辑查询语句，以及提供要创建和/或打开的 SQLite 数据库文件。

该变量的签名完全通过界面生成器（Interface Builder, IB）定义。请记住，你也可以通过编程方式创建相同的功能。

```swift
@IBOutlet weak var detailSQLiteQueryField: UITextView!
```

## 设置视图

当应用启动后首次加载详情场景时，`detailItem` 和 `configureView` 函数会从 `viewDidLoad` 函数中被调用。当从 `MasterViewController` 中调用 `prepareForSeque` 时，这些函数也会被调用。

我在此处所做的修改是，将 Master-Detail 模板中包含的 `UITextfield` 替换为 `detailSQLiteQueryField`，以便通过 `prepareForSegue` 函数接收信息，并添加代码来打开从 `MasterViewController` 的 TableView 中选中的数据库，同时传递 `detail.description` 的值。状态消息被赋给变量 `dbStatusMsg`，而不是直接传递给 `detailSQLiteQueryField`，因为当该函数被调用时，该字段尚未创建。因此，我在 `viewWillAppear` 函数中将 `dbStatusMsg` 的值赋给该字段。

```swift
var detailItem: AnyObject? {
    didSet {
        // 更新视图。
        self.configureView()
    }
}

func configureView() {
    if let detail = self.detailItem {
        if (dbDAO.openSQLiteDatabase(detail.description).rawValue == Enums.SQLiteStatusCode.ok.rawValue) {
            dbStatusMsg = "SQLite 数据库 \(detail.description) 已创建并打开"
        }
    }
}
```

### `viewDidLoad` 函数的实现

我替换了 `viewDidLoad` 函数中包含的所有代码，除了 `self.configureView`，并用一些设置代码来配置 `detailSQLiteQueryField`。

```swift
override func viewDidLoad() {
    super.viewDidLoad()
    // 从 nib 加载视图后，执行任何额外的设置。
    // 更新详情项的用户界面。
    self.detailSQLiteQueryField.layer.borderWidth = 1.0
    self.detailSQLiteQueryField.layer.borderColor = UIColor.gray().cgColor
    self.configureView()
}
```

### `viewWillAppear` 函数的实现

当 `viewWillAppear` 函数被调用时，我将 `dbStatusMsg` 的值赋值给 `detailSQLiteQueryField`，从而确保该字段确实可用。

```swift
override func viewWillAppear(_ animated: Bool) {
    self.detailSQLiteQueryField.text = dbname
}
```

## 构建 Winery 数据库

为了结束本章，我想演示如何创建一个数据库，我将在第 5-9 章中使用该数据库。这些章节将使用一个关于葡萄酒的 App 来执行增删改查（CRUD）操作。

### 创建 `Winery.sqlite` 文件



## 排版后的文本

这个任务其实非常简单。在 `detailSQLiteQueryField` 中输入 `Winery.sqlite` 文件名，然后点击 `Create/Open Database` 按钮（`createDbButton`）。我提供了来自 `DetailViewController` 中 `createDbButton` 函数以及 `DbMgrDAO` 类中 `openSQLiteDatabase` 函数的代码片段，以供参考。

在 `createDbButton` 函数中，调用了 `openSQLiteDatabase`，并传入了文件名 `Winery.sqlite` 作为必需的参数。你可能已经注意到，除了 SQLite 函数要求的基本错误检查外，没有额外的错误检查。同时，我乐观地假设返回码会是 `0` 或表示成功，并显示一条创建成功的消息。

```
@IBAction func createDbButton(sender: AnyObject) {
    dbname = self.detailSQLiteQueryField.text
    if(dbDAO.openSQLiteDatabase(dbname).rawValue == Enums.SQLiteStatusCode.ok.rawValue){
        self.detailSQLiteQueryField.text = "SQLite database \(self.detailSQLiteQueryField.text) is created and open"
    }
}
```

在 `openSQLiteDatabase` 中，你可能还记得，我获取了 Documents 目录的句柄，因为这是应用沙盒中少数几个可写入的位置之一，然后我调用了 SQLite 函数 `sqlite3_open` 来在指定路径创建和/或打开数据库。如果指定路径的文件名不存在，`sqlite3_open` 会创建数据库文件并打开它；否则，该函数会尝试打开数据库文件，并以此建立连接。

```
func openSQLiteDatabase(databaseName:String)->Enums.SQLiteStatusCode{
    let dirManager = FileManager.default()
    do {
        let directoryURL = try dirManager.urlForDirectory(FileManager.SearchPathDirectory.documentDirectory, in: FileManager.SearchPathDomainMask.userDomainMask, appropriateFor: nil, create: true)
        dbPath = try! directoryURL.appendingPathComponent(databaseName)
    } catch let err as NSError {
        print("Error: \(err.domain)")
    }
    return Enums.SQLiteStatusCode(rawValue: sqlite3_open(dbPath.absoluteString.cString(String.Encoding.utf8)!, &db))!
}
```

拿到数据库后，我现在将创建表和视图。

### 添加表

还记得 UI 中的 `Create Table` 和 `Create View` 按钮吗？如果你点击这些按钮，它们会将 `Db` 类中的模板加载到 `detailSQLiteQueryField` 字段中，以便进行编辑。一旦你调整好查询语句，只需点击 `Execute Query` 按钮（`executeQueryButton`），即可通过数据库引擎执行查询，并在已打开的数据库文件中创建数据库结构。

对于 `winery.sqlite` 数据库和应用程序，我需要两个表：`wine` 和 `producer`。我还可以轻松地扩展它，增加几个表、视图和触发器。这里我不深入讲解联接或外键，因为我会在第 5 章插入记录时详细讨论。简单来说，这两个表将具有外键关系，而视图将使用联接来访问两者的数据以进行显示。

**第 3 章 ■ 在运行时创建数据库**

我要创建的第一个表是 `producer` 表。该表将包含葡萄酒生产商的数据。第二个表 `wine` 将包含葡萄酒瓶的信息。表 3-1 概述了该架构。外键将位于 `wine` 表中，由 `Producer_id` 字段定义。

**表 3-1. Winery 架构**

| wine          | producer      |
|---------------|---------------|
| id            | id            |
| Producer_id   | name          |
| name          | country       |
| Rating        | region        |
|               |               |

要在 `winery.sqlite` 数据库中创建表，请按以下步骤操作：

1. 在运行的应用中（iOS 模拟器或安装在 iPad 上），在编辑器字段中输入 `"winery.sqlite"`，然后点击 `Create/Open Database` 按钮。
2. 如果数据库成功打开，你会收到一条成功消息，该消息会替换同一字段中的文本。
3. 接下来，点击 `Create Table` 按钮，将 `Create Table` 模板加载到编辑器字段中。
4. 修改查询语句，使其如下所示：

```
CREATE TABLE IF NOT EXISTS main.producer (
    Id INTEGER PRIMARY KEY AUTOINCREMENT NOT NULL UNIQUE,
    Producer_id integer,
    Name VARCHAR,
    Country VARCHAR,
    Region VARCHAR,
```



