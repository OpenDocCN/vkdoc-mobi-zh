# 处理多个数据库

要将 SQLite 数据库附加到打开的连接，首先需要打开一个初始数据库，从而建立与 SQLite 引擎的连接。这将作为主数据库，并且必须通过其架构 `main.table_name` 来引用该数据库。随后，你需要根据需要创建其他数据库，可以提前创建，也可以后续创建。在拥有这些数据库并打开第一个（即主）数据库之后，你可以像执行其他 SQL 查询一样发出 `ATTACH` 语句，指定要附加到主数据库连接的数据库名称和路径，以及别名或架构。

然后，你将通过其架构名称引用其他附加的数据库，例如 `seconddb.table_name` 或 `attacheddb.table_name`。附加数据库架构的名称是任意的，可以是任何你喜欢的名称，但必须是一个单词或复合词。© Kevin Languedoc 2016 [161]

K. Languedoc, *使用 Swift 和 SQLite 构建 iOS 数据库应用*, DOI 10.1007/978-1-4842-2232-4_10

## 附加内存数据库

你也可以附加内存数据库。我们在本书范围内没有探讨过内存数据库；但是，你可以在打开数据库时使用特殊的 `:memory:` 关键字来创建内存数据库，如下例所示：

```
sqlite3_open(":memory:", &db);
```

每次你使用上述命令创建内存数据库时，它都独立于其他任何数据库存在。创建内存数据库后，你可以像往常一样将其附加，如下所示：

```
let temp1 = "ATTACH DATABASE ':memory:' AS temp1;"
```

这将导致第二个内存数据库（创建的第二个）被附加到第一个（即主）数据库。你可以根据需要附加任意数量的内存数据库，只要记住它们将按照创建顺序被附加即可。

如果你使用的是临时数据库——同样，这超出了本书的讨论范围——你可以使用以下命令创建临时数据库：

```
sqlite3_open("", &db)
```

然后，像往常一样附加临时数据库：

```
let tempdb = "ATTACH DATABASE '' as temp1"
```

通过使用以下命令关闭 SQLite 连接，这两种类型的数据库都可以被终止存在：

```
sqlite3_close(&db)
```

此外，可以使用不同的架构名称多次附加同一个数据库。但是，你不能启用 `enable_cache_mode`，否则会收到错误。

## DETACH 语句

`DETACH` 语句执行与 `ATTACH` 数据库相反的功能。换句话说，该命令从打开的连接中分离先前附加的数据库。通过发出 `DETACH` 命令后跟架构名称来分离物理（磁盘上）数据库、内存数据库或临时数据库，如下所示：

```
let detach_db:String = "DETACH DATABASE schema_name"
```

与 `ATTACH` 语句一样，你可以使用相同的架构名称来引用要分离的数据库，从而分离被多次附加的同一个数据库（使用不同的架构）。与 `ATTACH` 操作一样，必须禁用 `enable_cache_mode`，否则会抛出错误。

## 多数据库限制

除了应用程序所在的 iOS 设备的存储空间物理限制之外，附加数据库的限制通过 `SQLITE_LIMIT_ATTACHED` 和 `sqlite3_limit` 命令设置。该命令的语法如下：

```
int sqlite3_limit(sqlite3*, int id, int newVal);
```

第一个参数是指向打开数据库连接的指针；第二个参数是限制类别之一（如下所列）；最后一个参数是附加数据库的新限制。对于附加数据库，你需要使用 `SQLITE_LIMIT_ATTACHED` 常量。

如果第三个参数使用负数，则不会对数据库限制进行任何更改。

- `SQLITE_LIMIT_LENGTH` 0
- `SQLITE_LIMIT_SQL_LENGTH` 1
- `SQLITE_LIMIT_COLUMN` 2
- `SQLITE_LIMIT_EXPR_DEPTH` 3
- `SQLITE_LIMIT_COMPOUND_SELECT` 4
- `SQLITE_LIMIT_VDBE_OP` 5
- `SQLITE_LIMIT_FUNCTION_ARG` 6
- `SQLITE_LIMIT_ATTACHED` 7
- `SQLITE_LIMIT_LIKE_PATTERN_LENGTH` 8
- `SQLITE_LIMIT_VARIABLE_NUMBER` 9
- `SQLITE_LIMIT_TRIGGER_DEPTH` 10

## 执行连接

构建考虑多个数据库的查询与在同一个数据库内构建表之间的连接完全相同。你需要记住的一个注意事项是，你需要通过使用数据库架构名称作为表的前缀来引用其他数据库中的表，如下例所示。

SQLite 支持三种类型的连接：`INNER JOIN`、`OUTER JOIN` 和 `CROSS JOIN`。这三种连接都可以使用附加到同一打开的连接的多个数据库来形成。请看这个例子：

```
SELECT c.name, a.address, a.city FROM mainDb.contacts c INNER JOIN secondDb.addresses a ON c.name, a.name
```

## 在 Swift 中实现附加与分离

这是一个简单的单视图 iPhone 应用，用于演示和测试 Swift 中的 `ATTACH` 和 `DETACH` SQLite 功能。该应用将提供一个函数来创建数据库（并打开它们）。还会有一个函数用于从主数据库 `ATTACH` 和 `DETACH` 一个数据库。

为简单起见，另一个函数将使用城市名称及其所在国家/地区的示例数据预填充数据库。最后一个函数将从数据库中获取数据，并在 `TableViewController` 中显示数据。

### 项目

创建项目后，我们需要像之前看到的那样添加 `sqlite3` 库；它位于 **通用** 设置的 **已链接库和框架** 部分。

库就位后，我们将使用 **Objective-C 类** 模板并选择 **类别** 文件类型来添加桥接，这将触发 Xcode 为我们创建桥接文件，并为 Swift 编译器设置 **构建设置**。当然，我们仍然需要在桥接文件中添加 `#import <sqlite3.h>` 头文件引用，以便与 `sqlite3` 库实际交互。

项目设置完成后，我们可以向 `ViewController` 添加逻辑来处理多数据库操作。

### ViewController

`ViewController` 是应用逻辑的主要入口点。除了通常需要与 SQLite 交互的全局变量和常量外，`ViewController` 将有六个函数。我们将在以下小节中探讨这些函数。

由于故事板中的主视图将包含一个用于已创建数据库的 `UIPickerView`，我们需要添加委托和数据源协议：

```swift
class ViewController: UIViewController, UIPickerViewDelegate, UIPickerViewDataSource
```

### 全局变量和常量

当使用 **单视图应用** 模板创建应用时，默认会创建 `ViewController`。为了使用 SQLite，我们需要一个用于数据库名称和路径的变量，在本应用中称为 `dbPath`。`databases` 数组将包含数据库名称，以便我们稍后可以选择一个数据库附加到主数据库。

我们还需要一个指针，即 `COpaquePointer` 变量，用于 SQLite 数据库连接，以及一个用于 SQLite 3 语句的变量。它们分别命名为 `db` 和 `sqlStatement`。`SQLITE_TRANSIENT` 是 SQLite 析构函数的一个可移动指针，它使用 `sqlite3_destructor_type` 类型。这需要在 Swift 中使用 `unsafeBitCast` 函数进行转换。

```swift
internal let SQLITE_TRANSIENT = unsafeBitCast(-1, to: sqlite3_destructor_type.self)
var databases = [String]()
var dbPath = URL()
var db: COpaquePointer? = nil
var sqlStatement: COpaquePointer? = nil
var attachDb: String = ""
```

### IBActions 和 IBOutlets

尽管 `IBActions` 和 `IBOutlets` 稍后将通过 UI 故事板添加，但我想提一下......



