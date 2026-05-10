# SQLite SELECT 语句

`SQLite SELECT`语句用于从`SQLite`数据库中提取数据和信息。`SELECT`语句是`SQL`语言中最复杂的语句，主要是因为其各种排列组合方式。

### 基本语法

`SELECT`查询的基本语法如下：

```
SELECT column(s) from main.table
```

你也可以使用通配符返回表中的所有列：

```
SELECT * from main.table
```

如果你只需要返回表中的一部分数据子集，可以使用`WHERE`子句，如以下示例所示：

```
SELECT * from main.table WHERE column1 = ‘some value’
```

`WHERE`子句可以评估自定义函数或标准函数。你也可以使用`IN`子句。例如：

```
SELECT id, column1 FROM main.table WHERE column1 IN (SELECT col_id, column1 from main.second_table WHERE col_id=id)
```

`SELECT`语句也可以包含另一个`SELECT`语句来代替某个列；这被称为内联查询，如下所示：

```
SELECT id, column1, (SELECT column FROM main.table)
```

### 选择数据

第一个用例将演示如何执行一个基本的、模板化的不带`WHERE`子句的`SELECT`查询。`SQLite`中的`SELECT`操作通过`select-stmt`函数执行。虽然你不直接与该函数交互（即执行`SELECT`查询时不直接调用此函数），但该函数支持`SELECT`的标准`SQL API`。以下示例遵循相同的模式：访问`Documents`路径中的`SQLite`数据库，打开它，然后执行`SELECT`查询。

在此示例中，`SELECT`查询获取一个联系人结果集，并将其分配给一个自定义的`Swift`数据类型。`SELECT`查询字符串使用`Swift String`变量定义。查看`sqlite3_open`语句中的`database`参数，作为如何使用`NSString`构建`SELECT`查询并将其传递给`sqlite3_stmt`函数以供数据库引擎执行的示例。

`SELECT`语句的`SQL`查询在执行前会被传递给`sqlite3_prepare_v2`函数。要访问结果集，你需要使用`sqlite3_step`函数以及`SQLITE_ROW`常量来循环遍历结果；只要结果集中还有记录，`SQLITE_ROW`就保持为`true`。

一旦结果集耗尽，调用`sqlite3_finalize`来清理准备好的语句，然后调用`sqlite3_close`关闭数据库。字符串数据使用`UTF-8`编码存储在数据库中，因此当你需要将字符串值分配给`Swift`变量时，需要使用`String`类的`NSUTF8StringEncoding`方法。

**第 6 章 ■ 选择记录**

从结果集中检索到的数据库值必须使用特殊函数（如`sqlite3_column_text`）传递给或分配给变量或自定义数据类型。每种数据类型（包括`blob`）都有对应的`sqlite3_column`函数。对表中列的访问通过零基数组实现，如以下示例代码所示。

当值被分配给`Swift`变量或自定义数据类型属性后，如果你想在`UIPickerView`或`UITableView`中显示它们，需要将这些值添加到数组或`NSDictionary`中，这是这些基于列表的对象首选且最常用的数据源方法。参见下面的示例代码：

```
class SelectWithSwift: NSObject {

    var dbPath:URL = URL()
    let db:COpaquePointer?=nil
    let sqlite3_stmt:COpaquePointer?=nil

    override init() {
        let dirManager = FileManager.default()
        do {
            let directoryURL = try dirManager.urlForDirectory(FileManagerSearchPathDirection.documentDirectory, in FileManager.SearchPathDomainMask.UserDomainMask, appropriateFor: nil, create: true)
            dbPath = try! directoryURL.appendingPathComponent("Contacts.sqlite")
        } catch let err as NSError {
            print("错误: \(err.domain)")
        }
    }

    func simpleSelect(){
        var contactList = [Person]()
        let sql:String = "Select id, name, address, city, zip,country from contact"
        if(sqlite3_open(dbPath.path!, &db)==SQLITE_OK){
            if(sqlite3_prepare_v2(db, sql.cString (using:String.Encoding.utf8)!, -1,
```



```sql
&sqlStatement, nil) == SQLITE_OK)

{

while (sqlite3_step(sqlStatement)==SQLITE_ROW) {

let contact:Person = Person()

contact.name = String(cString:UnsafePointer<Int8>(sqlite3_column_

text(sqlStatement, 0)))!

contact.address = String(cString:UnsafePointer<Int8>(sqlite3_column_

text(sqlStatement, 1)))!

contact.city = String(cString:UnsafePointer<Int8>(sqlite3_column_

text(sqlStatement, 2)))!

contact.zip = String(cString:UnsafePointer<Int8>(sqlite3_column_

text(sqlStatement, 3)))!

contact.country = String(cString:UnsafePointer<Int8>(sqlite3_column_

text(sqlStatement, 4)))!contactList.append(contact)

}

}
```

