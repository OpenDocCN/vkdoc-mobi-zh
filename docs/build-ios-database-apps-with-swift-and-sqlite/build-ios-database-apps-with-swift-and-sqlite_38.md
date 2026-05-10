# 删除记录

## 使用 `sqlite3_bind_int` 示例

```
sqlite3_bind_int(sqlStatement, 1, 1)

if(sqlite3_step(sqlStatement)==SQLITE_DONE){
    print("item deleted")
    sqlite3_finalize(sqlStatement)
    sqlite3_close(db)
}else{
    print("unable to delete")
}
```

或者，我们可以使用 `sqlite3_exec` 函数执行相同的查询操作，如下例所示。你可能还记得，`sqlite3_exec` 函数封装了 `sqlite3_prepare_v2`、`sqlite3_step` 和 `sqlite3_finalize` 函数。参见此处：

```
func sampleExecDelete(){
    let deleteStmt:String = "DELETE FROM sampleTable WHERE id = ?"
    if(sqlite3_open(dbPath.path!, &db)==SQLITE_OK){
        if(sqlite3_exec(db, deleteStmt, nil, &sqlStatement, errMsg)==SQLITE_OK){
            sqlite3_bind_int(sqlStatement, 1, 1)
            sqlite3_close(db)
        }else{
            print("unable to delete")
        }
    }
}
```

## 为 Winery 应用添加删除功能

这是我们在构建的 CRUD 应用的最后一章。要开始为我们的应用添加 DELETE 功能，请按如下代码所示，打开标头并打开 `deleteRecords` 方法。`deleteRecords` 方法将只接受一个参数作为 WHERE 子句变量。

要在 Winery 应用中实现 DELETE 功能，我们需要对各个视图控制器、表视图控制器以及 storyboard 中的 `UITableViewControllers` 进行几处修改。我们还需要向 `WineryDAO` 类添加两个函数。

### 修改 WineryDAO 类

在 `WineryDAO` 类中，我们需要添加两个函数来处理 `Wine` 表中的删除以及 `Winery` 表中的删除。这两个函数都将对 winery 数据库执行 DELETE 语句。这两个函数将使用两种不同的方法实现相同的设计。实际上，这段代码可以用一个函数替换，并将查询字符串作为输入参数传递，但我决定对第二个函数使用 `sqlite3_exec`，以展示在 SQLite 中执行查询的两种方式。

#### 添加 `deleteWineRecord` 函数

`deleteWineRecord` 函数包含一个定义 DELETE SQL 语句的字符串常量，并接受一个参数作为 WHERE 子句。

SQLite 执行操作包括使用 `sqlite3_open` 函数打开 SQLite 数据库，如果数据库成功打开，则接着调用 `sqlite3_prepare_v2` 函数。

如果 SQL 语句没有错误且成功加载到内存中，则使用 `sqlite3_bind_text` 函数将参数传递给查询，最后使用 `sqlite3_step` 函数执行查询。

我将照常测试该函数并在本章末尾发布结果。

```
func deleteWineRecord(record:String){
    let deleteSQL = "DELETE FROM wine WHERE name = ?"
    if(sqlite3_open(dbPath.path!, &db)==SQLITE_OK){
        if(sqlite3_prepare_v2(db, deleteSQL, -1, &sqlStatement, nil)==SQLITE_OK){
            sqlite3_bind_text(sqlStatement, 1, record, -1, SQLITE_TRANSIENT)
            if(sqlite3_step(sqlStatement)==SQLITE_DONE){
                print("item deleted")
            }else{
                print("unable to delete")
            }
        }
    }
}
```

#### 添加 `deleteWineryRecord` 函数

与上一个函数类似，`deleteWineryRecord` 将从 SQLite 数据库中删除在 `WineryListTableViewController` 中选择的记录。函数体的第一行包含一个定义 SQL DELETE 语句的 SQL 字符串常量。注意，根据 API 要求，表定义中省略了模式。

接下来，使用 `sqlite3_open` 打开数据库，然后使用 `sqlite3_exec` 函数，该函数封装了 `sqlite3_prepare_v2`、`sqlite3_step` 和 `sqlite3_finalize` 的执行 API。

```
func deleteWineryRecord(record:String){
    let deleteSQL = "DELETE FROM winery WHERE name = ?"
    if(sqlite3_open(dbPath.path!, &db)==SQLITE_OK){
        if(sqlite3_exec(db, deleteSQL, nil, &sqlStatement, errMsg)==SQLITE_OK){
            sqlite3_bind_text(sqlStatement, 1, record, -1, SQLITE_TRANSIENT)
            sqlite3_close(db)
        }else{
            print("unable to delete")
        }
    }
}
```

## 修改视图控制器

对于应用的这一部分，我们不需要对 `FirstViewController` 或 `SecondViewController` 进行任何更改。

## 修改表视图控制器

添加删除功能需要对 `WineryListTableController` 和 `WineListTableViewController` 进行几处更改。我们必须在菜单中启用编辑按钮，并在 `WineDAO` 类中调用删除函数。

#### WineryListTableViewController

对于 `WineryListTableViewController`，我们在 `viewDidLoad` 方法中、`loadWineList()` 函数下方启用编辑按钮。`edit.editButtonItem` 被分配给 `self.navigationItem.rightBarButtonItem` 方法，应用运行时您将能看到它。

```
override func viewDidLoad() {
    super.viewDidLoad()
    loadWineList()
    // Uncomment the following line to display an Edit button in the navigation bar for this view controller.
    self.navigationItem.rightBarButtonItem = self.editButtonItem()
}
```

第二个更改是启用以下 tableView 标准函数。当我们创建 `UITableViewController` 的子类时，代码已经包含在内；我们只需要取消注释即可启用它。

然而，我们仍然需要添加一些代码来实际从数据库中删除行，因为 `deleteRowsAtIndexPath` 会从数组中移除一个项目，但如果我们不从数据库中删除同一行，当视图刷新时，该记录将重新出现。我们通过获取所选记录的行索引并从 `wineries` 数组中检索该记录来实现这一点。然后，我们创建一个 `winery` 类的实例对象，并将 winery 的名称传递给 `deleteWineryRecord` 函数。

我们还在删除操作的开头和结尾添加了 `tableView.beginUpdates` 和 `tableView.endUpdates`。这些表示后续操作被执行和终止。

```
override func tableView(tableView: UITableView, commitEditingStyle editingStyle: UITableViewCellEditingStyle, forRowAtIndexPath indexPath: NSIndexPath) {
    if editingStyle == .delete {
        tableView.beginUpdates()
        // Delete the row from the data source
        let winery:Wineries = wineryListArray[(indexPath as NSIndexPath).row]
        tableView.deleteRows(at: [indexPath], with: .fade)
        wineDAO.deleteWineryRecord(winery.name)
        tableView.endUpdates()
    } else if editingStyle == .Insert {
        // Create a new instance of the appropriate class, insert it into the array, and add a new row to the table view
    }
}
```

#### WineListTableViewController

类似地，我们为 `WineListTableViewController` 实现编辑按钮，就像我们为之前的 `TableViewController` 所做的那样：

```
override func viewDidLoad() {
    super.viewDidLoad()
    self.loadWineList()
    self.navigationItem.rightBarButtonItem = self.editButtonItem()
}
```

再次，我们实现以下 `tableView` 函数以启用 `TableView` 的删除功能。在 `delete` 编辑样式中，我们包围了 `tableView.beginUpdates` 和 `tableView.endUpdates`，并从 `wineListArray` 中获取行索引，然后创建一个实例对象并将所选记录的 `name` 值传递给 `deleteWineRecord` 函数。

```
override func tableView(tableView: UITableView, commitEditingStyle editingStyle: UITableViewCellEditingStyle, forRowAtIndexPath indexPath: NSIndexPath) {
    if editingStyle == .delete {
        tableView.beginUpdates()
        let wine = wineListArray[(indexPath as NSIndexPath).row]
        // Delete the row from the data source
        tableView.deleteRows(at: [indexPath], with: .fade)
        wineDAO.deleteWineRecord(wine.name)
        tableView.endUpdates()
    } else if editingStyle == .Insert {
        // Create a new instance of the appropriate class, insert it into the array, and add a new row to the table view
    }
}
```

有了这些代码，现在只需要运行应用并进行测试。

## 修改删除功能的 UI

### 修改 UI



