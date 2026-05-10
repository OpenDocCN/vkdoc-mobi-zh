# SQLite C API 数据绑定

SQLite C API 提供了专门用于处理数据的函数，允许应用程序将数据与 Cocoa Touch 的相应数据类型进行绑定。绑定后的数据类型可用于预编译语句。以下函数涵盖了文本和数字相关的所有主要基本数据类型。对于二进制大对象（blob）、图像、视频和音频，API 提供了 `blob` 和 `blob64`，以及 `zero` 和 `zeroblob64`。

```c
int sqlite3_bind_blob(sqlite3_stmt*, int, const void*, int n, void(*)(void*));
int sqlite3_bind_blob64(sqlite3_stmt*, int, const void*, sqlite3_uint64, void(*)(void*));
int sqlite3_bind_double(sqlite3_stmt*, int, double);
int sqlite3_bind_int(sqlite3_stmt*, int, int);
int sqlite3_bind_int64(sqlite3_stmt*, int, sqlite3_int64);
int sqlite3_bind_null(sqlite3_stmt*, int);
int sqlite3_bind_text(sqlite3_stmt*, int, const char*, int n, void(*)(void*));
int sqlite3_bind_text16(sqlite3_stmt*, int, const void*, int, void(*)(void*));
int sqlite3_bind_text64(sqlite3_stmt*, int, const char*, sqlite3_uint64, (*)(void*), unsigned char encoding);
int sqlite3_bind_value(sqlite3_stmt*, int, const sqlite3_value*);
int sqlite3_bind_zeroblob(sqlite3_stmt*, int, int n);
int sqlite3_bind_zeroblob64(sqlite3_stmt*, int, sqlite3_uint64);
```

对于基本的原始数据类型，SQLite 绑定函数的第一个参数是使用 `sqlite3_stmt` 函数创建并从 `sqlite3_prepare_v2` 函数返回的语句。第二个参数是要插入记录的表中的列号，对应一个从零开始的数组，最后一个参数是要插入的值。这些列是一个从 0（零）开始的数组。因此，第 1 列对应 0，第 2 列对应 1，以此类推。

对于二进制大对象（blob）和 64 位长度的函数，第四个参数是要插入的值的字节数，而不是字符数。你使用 blob 来存储图像、视频和音频数据。最后一个参数是析构函数。

## SQLite INSERT 函数

`INSERT` 函数是 SQL `INSERT` 语句的 SQLite 实现。SQLite `INSERT` 语句有三种变体：

- **不指定列名插入值**：你可以不指定列名就将值插入到数据库表中。但是，插入的值必须与表中的列数匹配。这种插入形式如下：

```sql
INSERT into main.table VALUES(values list items)
```

- **使用 SELECT 语句插入**：第二种插入类型使用 `SELECT` 语句作为要插入的行列表。如果在 `INSERT` 语句中省略了列名，那么 `SELECT` 语句必须与目标表具有完全相同数量的列，除非表列具有默认值。以下是第二种类型的三个示例：

```sql
INSERT INTO main.table
SELECT * FROM main.otherTable WHERE clause

INSERT INTO main.table
SELECT column list FROM main.otherTable (with or without WHERE clause)

INSERT INTO main.table(column list)
SELECT column list FROM main.otherTable (with or without WHERE clause)
```

以下是一个关于如何使用 SQLite C API 和 Swift 插入记录的快速、样板式示例：

```swift
internal let SQLITE_STATIC = unsafeBitCast(0, to: sqlite3_destructor_type.self)
internal let SQLITE_TRANSIENT = unsafeBitCast(-1, to: sqlite3_destructor_type.self)

func sample(){
    var sqlite3_stmt:COpaquePointer?=nil;
    var sqlite3_db:COpaquePointe?r=nil;
    var txt:String = "some text";
    let integer:Int32 = 500;
    let dbl:Double = 10.45;
    var dbPath:URL = URL()
    var sqlStatement:COpaquePointer?=nil
    var dbErr: UnsafeMutablePointer<UnsafeMutablePointer<Int8>>? = nil
    var errmsg:String=""
    let dbName = "winery.sqlite"
    
    //insert query
    let sql:String = "INSERT INTO table(coltext, colint, coldouble) VALUES(?,?,?)";
    let dirManager = FileManager.default()
    
    //Open db assuming there are no subfolders
    do {
        let documentDirectoryURL = try dirManager.urlForDirectory(FileManager.
```

---
© Kevin Languedoc 2016 [63]
K. Languedoc, *Build iOS Database Apps with Swift and SQLite*, DOI 10.1007/978-1-4842-2232-4_5



`SearchPathDirectory.documentDirectory`，位于`FileManager.SearchPathDomainMask.userDomainMask`，`appropriateFor: nil`，`create: true`）

```swift
dbPath = documentDirectoryURL.urlLByAppendingPathComponent(dbName)
} catch let err as NSError {
    print("Error: \(err.domain)")
}
sqlite3_open(dbPath.path!, &sqlite3_db);
sqlite3_prepare_v2(sqlite3_db, sql, 1, &sqlite3_stmt, nil);
sqlite3_bind_text(sqlite3_stmt, 1, txt.cString(using: String.encoding.ut)!, -1, SQLITE_TRANSIENT);
sqlite3_bind_int(sqlite3_stmt, 2, integer);
sqlite3_bind_double(sqlite3_stmt, 3, dbl);
sqlite3_step(sqlite3_stmt);
sqlite3_finalize(sqlite3_stmt);
sqlite3_close(sqlite3_db);
```

在上述代码中，`SQLITE_STATIC`是一个不可变的`sqlite3_static`值指针，而`SQLITE_TRANSIENT`是一个未来会发生变化的指针，但实际移动指针的是 SQLite。

在函数内部，我使用`COPaquePointer`创建了一个名为`sqlite3_stmt`的 SQLite 语句变量。该变量将通过`sqlite3_prepare_v2`函数与字符串查询`sql`以及同样使用`COPaquePointer`创建的 SQLite 数据库引擎变量`db`一起初始化。接下来的三行代码创建了一些测试变量，以便向数据库中插入值。为清晰和简洁起见，我创建了`String`、`Int32`和`Double`类型的变量，并硬编码了一些值。不过，这些值可以通过代码块、方法参数或某些处理/计算结果动态设置。

实际的 SQL INSERT 查询语句非常标准，正如`sql`字符串变量所示——稍后需使用`cUsingStringEncoding`和`NSUTF8Encoding`将其转换为 C 的`char`（字符字符串）。我发现这种方式效果最佳。有些开发者曾试图用实际变量名替换问号，但这样代码会变得非常混乱且难以维护，并且 SQLite 常常会报错，因为你需要转换并绑定输入值。

接下来的关键部分是数据库文件的路径。在本例中，我通过`NSFileManager`类的`NSSearchPathDirectory.DocumentDirectory`和`GetDirectory`函数获取路径并拼接文件名。我使用`URLByAppendingPathComponent`函数（该方法由`stringByAppendingPathComponent`更改而来）来拼接数据库文件名。上述代码假设没有子文件夹，否则我需要使用`NSSearchPathForDirectoriesInDomains`函数将文档文件夹和子文件夹存储到一个数组中。

### 插入记录

另一条重要信息是：你必须在应用沙盒的 Documents 目录中创建数据库。这是唯一可用的可写目录。如果你将数据库放在 Resources 文件夹中，虽然不会报错，但数据不会被写入，因为该文件夹是只读的。

### 插入或替换

SQLite 还提供了一个特殊子句，允许你通过 INSERT 语句执行插入/更新操作。在 SQLite 中，你可以使用`INSERT OR REPLACE`语句。如果遇到插入约束，`REPLACE`子句会删除现有记录并替换为新记录。当存在`NOT NULL`约束且`REPLACE`试图插入`NULL`值时，它会尝试用默认值替换；如果默认值不可用，则该行会使用`ABORT`子句。除非遇到类似条件，否则其余行不受影响。一般语法如下：

```sql
INSERT OR REPLACE INTO schema.table_name(Id, ColText, ColInt, ColDouble) VALUES(1, 'Kevin', 20, 53.6)
```

如果索引值`Id = 1`不存在，则插入该记录；反之，如果已存在，则更新该记录。插入记录时，你可以使用 Swift 数据类型替代基本类型，如下例所示：

```swift
func replace() {
    let index: Int32 = 1
    let name: String = "kevin"
    let Dbl: Double = 1000.99
```



```swift
var dbPath: URL = URL();

let dirManager = FileManager.default()

do {

let directoryURL = try dirManager.urlForDirectory(FileManager.SearchPathDirectory.documentDirectory, in: FileManager.SearchPathDomainMask.userDomainMask, appropriateFor: nil, create: true)

dbPath = directoryURL.urlByAppendingPathComponent("database.sqlite")

if(sqlite3_open(dbPath.absoluteString?.cString(using: String.Encoding.utf8)!, &sqlite3_db) == 0){

let sql: String = "INSERT OR REPLACE INTO schema.simpletable (id, name, colDouble) VALUES(\(index),\(name.cString(using: String.Encoding.utf8)), \(Dbl))"
if(sqlite3_prepare_v2(sqlite3_db, sql.cString(using: String.Encoding.utf8)!, -1, &sqlite3_stmt, nil) != SQLITE_OK)

{

print("预编译语句出现问题")

}else{

sqlite3_finalize(sqlite3_stmt);

sqlite3_close(sqlite3_db);

}

}

} catch let err as NSError {

print("错误: \(err.domain)")

}

}
```

此查询与上一个类似，不同之处在于我使用插值来构造一个新的查询字符串。

### Insert or Rollback
回滚选项提供了一种优雅的方式，以便在事务进程不如预期时退出事务，例如，插入到列中的数据出现问题。

`OR REPLACE` 是 `ON CONFLICT REPLACE` 子句的简写形式，与其他冲突处理子句 `ABORT`、`REPLACE`、`IGNORE`、`FAIL` 类似。例如，你可能不希望替换现有记录。如果数据库中已存在重复记录，你可能希望在不生成错误的情况下回滚该事务。其通用语法如下：

```swift
let sql: String = "INSERT or ROLLBACK INTO table(coltext, colint, coldouble) VALUES(?,?,?)";
```

再次说明，此查询假设你将使用 SQLite3 数据绑定来将数据绑定到值，这是在处理 SQLite3 时最安全的方式或模式。

### Insert or Ignore
`IGNORE` 子句通过完全跳过有问题的行来处理插入约束，并像其他约束子句（`REPLACE` 除外）一样生成 `SQLITE_CONSTRAINT` 错误。其前后的行会正常处理，除非遇到其他约束。请参见以下示例：

```swift
let sql: String = "INSERT or IGNORE INTO table(coltext, colint, coldouble) VALUES(?,?,?)";
```

### Insert or Abort
`ABORT` 子句会中止当前操作并退出当前事务，从而允许你的程序继续处理其他潜在的插入操作或继续执行运行时逻辑。其语法与其他约束子句相同：

```swift
let sql: String = "INSERT or ABORT INTO table(coltext, colint, coldouble) VALUES(?,?,?)";
```

### Insert or Fail
最后一个子句是 `FAIL`。当遇到约束并调用 `SQLITE_CONSTRAINT` 时，该子句会被触发。之前的事务得以保留，但当前事务被取消，且后续事务不会发生。示例如下：

```swift
let sql: String = "INSERT or FAIL INTO table(coltext, colint, coldouble) VALUES(?,?,?)";
```

### 插入 Blob（二进制大对象）
在 Swift 和 Cocoa Touch 框架中处理二进制数据意味着你需要在 iOS 端使用 `NSData` 类，在 SQLite 端使用 `blob`。本部分我们将介绍两个函数，它们允许你将任意二进制数据插入到 SQLite 数据库中。虽然技术上可行，但我建议不要在生产应用中使用这种方法，因为它会消耗大量宝贵的存储空间。在某些场景下，你可能需要在项目中提供二进制数据，且这些数据不会随时间推移而增大体积。

以下两个函数可用于插入视频、图像和音频等二进制数据。在这两个函数中，`sqlite3_bind_blob` 几乎总是会被用到。而 `sqlite3_bind_zeroblob` 则用于将 blob 增量写入数据库，而非一次性将完整大小的 blob 存储到内存中。我们将对两者进行演示：



`int sqlite3_bind_blob(sqlite3_stmt*, int, const void*, int n, void(*)(void*)); int sqlite3_bind_zeroblob(sqlite3_stmt*, int, int n);`

第一个参数是 SQLite INSERT 语句，第二个参数是要设置的参数的索引，第三个参数是要绑定到语句的值。如果该值为`nil`，则忽略第四个参数；否则，该参数是缓冲区中的字节数，代表二进制数据的长度。最后一个（即第五个）参数是一个析构函数，用于在 blob 插入数据库后将其销毁。以下代码提供了一个参考实现：

```
func insertBlob(){

    var dbPath:URL = URL()

    var bindata:Data = Data()

    var imagePath:String = ""

    var urlObj:URL = URL()

    let binName:String = "Test image"

    let dirManager = FileManager.default()

    do {

        let directoryURL = try dirManager.urlForDirectory(FileManager.SearchPathDirectory.documentDirectory, in: FileManager.SearchPathDomainMask.userDomainMask, appropriateFor: nil, create: true)

        dbPath = directoryURL.urlByAppendingPathComponent("database.sqlite")

        if( sqlite3_open(dbPath.absoluteString?.cString(using: String.Encoding.utf8)!,&sqlite3_db) == 0){

            let sql:String = "Insert into binaryTbl(PictureName, ImageData) VALUES(?,?)"

            if(sqlite3_prepare_v2(sqlite3_db, sql.cString (using: String.Encoding.utf8)!, -1, &sqlite3_stmt, nil) != SQLITE_OK)

            {

                print("Problem with prepared statement")

            }else{

                imagePath = Bundle.main.pathForResource("IMG_0095", ofType: "JPG")!

                urlObj = URL(fileURLWithPath: imagePath)

                bindata = try! Data(contentsOf: urlObj)!

                sqlite3_bind_text(sqlite3_stmt, 1, binName.cString (using: String.Encoding.utf8)!, -1, SQLITE_TRANSIENT);

                sqlite3_bind_blob(sqlite3_stmt, 2, bindata.bytes,Int32(bindata.count), SQLITE_TRANSIENT);

                if(sqlite3_step(sqlite3_stmt)==SQLITE_DONE){

                    sqlite3_finalize(sqlite3_stmt);

                    sqlite3_close(sqlite3_db);

                }

            }

        }

    } catch let err as NSError {

        print("Error: \(err.domain)")

    }

}
```

从上述代码可以看出，其模式与其他 SQLite 代码片段类似。首先，我们使用`var`关键字创建变量，或使用`let`关键字创建常量。如果犯了错误，Xcode 会提示并提供修复建议。变量（或常量）的可见性取决于你的需求。

创建好变量和常量后，像往常一样尝试打开 SQLite 数据库并设置 SQL 查询。使用`sqlite3_prepare_v2`函数执行查询，然后将数据绑定到相应的列。

绑定二进制数据与绑定其他数据类型类似，但多了几个步骤。要处理二进制数据（图像、视频、编译后的文件），需要使用`NSData`，这是 Foundation 框架中的一个类，为字节缓冲区提供包装。

当存储二进制数据时，你实际上是在存储字节。要获取这些字节，使用`NSURL`类获取文件路径，并通过`NSData contentsOfURL`从文件中读取字节。`sqlite3_bind_blob`除了需要`sqlite3_statement`指针、列位置和`SQLITE_TRANSIENT`指针外，还需要文件的字节内容以及字节缓冲区的长度。

**创建 Winery 应用**

Winery iPhone 应用将允许用户输入他们最喜欢的葡萄酒信息，并拍摄酒标或酒瓶的照片。这些信息将存储在 SQLite 数据库中。在后续章节中，我们将扩展该应用，以包含其他 CRUD（创建或插入、读取或选择、更新和删除）操作。

在本章中，我将创建初始应用，并添加葡萄酒信息和酒庄信息的插入功能。该应用将基于“选项卡式应用”模板。

**创建项目**

在 Xcode 中，创建一个新项目，并在 iOS 应用程序类别下选择“选项卡式应用”模板。

> **注意**：如果你不熟悉 Xcode，可以在没有项目打开时从启动器创建项目，或者通过“文件”菜单 ➤ “新建” ➤ “项目”来创建。



