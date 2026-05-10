# 在 SQLite 中更新记录

在 SQLite 中更新记录与在 SQL Server 或 Oracle 等其他平台上使用 `UPDATE` 语句非常相似。不过，SQLite 还提供了与 `INSERT` 语句性质类似的额外更新记录操作。

在本章中，我们将讨论在 SQLite 中使用 `UPDATE` 语句的各种方式。其中包括以下内容：

- `UPDATE` 语句
- `WHERE` 子句
- `LIMIT` 和 `ORDER BY` 子句
- 使用子查询
- 使用连接
- 在子查询中使用 `WHERE` 子句
- 使用 `ON Conflict` 子句
- `OR FALLBACK` 语句
- `OR ABORT` 语句
- `OR REPLACE` 语句
- `OR FAIL` 语句
- `OR IGNORE` 语句

本章的后续部分将为上述每种操作提供可实际运行的查询示例，同时还将为 Winery 应用添加更新功能。

## SQLite 更新语句

`UPDATE` 语句用于更新 SQLite 数据库中的现有记录。如果只需要更新表中部分行的子集，可以使用 `WHERE` 子句来筛选或定位需要更新或替换的记录（稍后将介绍）。`LIMIT` 子句和 `ORDER BY` 子句可用于限制受 `UPDATE` 影响的行数。同样，`ORDER BY` 主要用于在使用 `LIMIT` 子句时确定哪些行会被包含在内。

`WHERE`、`LIMIT` 和 `ORDER BY` 是可选的；但 `WHERE` 子句几乎总是会被用到。表名应包含架构名称，尤其是在同一文件附加了多个数据库的情况下。请参考以下内容：

© Kevin Languedoc 2016 [113] K. Languedoc, *Build iOS Database Apps with Swift and SQLite*, DOI 10.1007/978-1-4842-2232-4_7

```
UPDATE main.tableName
SET column(s) = expression
WHERE whereClause
LIMIT numer_of_rows
ORDER BY column(s)
```

可与 `UPDATE` 一起使用的其他选项包括 `OR` 子句，具体有 `ROLLBACK`、`FAIL`、`ABORT`、`IGNORE` 和 `REPLACE`。

我们将要分析的第一个查询示例是基础的 `UPDATE` 语句。如果你曾在 SQL 中或 SQL 语言的其他平台衍生版本（如 PL/SQL 或 T-SQL）中使用过 `UPDATE`，那么你会感到很熟悉，因为 `UPDATE` 在 SQLite 中的工作方式完全相同：

```
UPDATE main.CityTemperature
SET Temperature = 1
, Scale = 'C'
, Date = '11-15-2014'
```

在上例中，`CityTemperature` 表被更新以反映当前的温度读数。这里假设数据库中只有一条记录；否则所有条目都会随之被更新。

在 iOS 应用程序中，`UPDATE` 语句可以使用从 UI 字段（`IBOutlet`）捕获的值，并通过 SQLite 绑定类型将这些值传递给查询语句，正如我们之前所见。沿用上例，假设有三个字段（`UITextField`）通过 `IBOutlet` 连接到 `ViewController`。代码大致如下：

```
func updateRecords(){
    let sql:String = "Update main.data set coltext=?"
    if(sqlite3_open(dbPath.path!, &db)==SQLITE_OK){
        if(sqlite3_prepare_v2(db, sql.cString(using: String.Encoding.utf8)!, -1,
                              &sqlStatement, nil) == SQLITE_OK)
        {
            sqlite3_bind_text(stmt, 1, txt.cString(using: String.Encoding.utf8)!, -1,
                              SQLITE_TRANSIENT);
        }
    }
    sqlite3_step(stmt);
    sqlite3_finalize(stmt);
    sqlite3_close(cruddb);
}
```

在此示例中，我定义了一个 Swift `String` 常量，其中包含待更新列新值的占位符。打开数据库后，查询字符串被附加到 `sqlite3_prepared_v2` 函数，值则通过 `sqlite3_bind_text` 进行绑定。

尽管这个查询可以工作，但不太实用，因为它要么假设数据库中只有一条记录，要么所有行都会被更新为这个新值。下一个示例将使用 `WHERE` 子句来限定更新范围，使其仅作用于一组共同的行或单一行（视情况而定）。

## 使用 WHERE 子句进行更新



为了更精确地执行`UPDATE`语句，通常需要与`WHERE`子句结合使用。该子句位于`UPDATE`语句的末尾，如下例所示。当然，也可以通过使用`AND`运算符甚至标准或自定义函数，在`WHERE`子句中包含多个布尔表达式。

```sql
UPDATE CityTemperature
SET Temperature = 1
    , Scale = 'C'
    , Date = '11-15-2014'
WHERE City = 'Montreal'
```

在 Swift 中，这个查询可以表示如下：

```swift
func updateRecords() {
    let sql: String = "Update Temperature set TemperatureScale =? where City=? and Country = ?"
    if (sqlite3_open(dbPath.path!, &db) == SQLITE_OK) {
        if (sqlite3_prepare_v2(db, sql.cString(using: String.Encoding.utf8)!, -1, &sqlStatement, nil) == SQLITE_OK) {
            sqlite3_bind_text(stmt, 1, txt.cString(using: String.Encoding.utf8)!, -1, SQLITE_TRANSIENT);
            sqlite3_bind_text(stmt, 2, utxt.cString(using: String.Encoding.utf8)!, -1, SQLITE_TRANSIENT);
            sqlite3_bind_text(stmt, 3, txt.cString(using: String.Encoding.utf8)!, -1, SQLITE_TRANSIENT);
        }
        sqlite3_step(stmt);
        sqlite3_finalize(stmt);
        sqlite3_close(cruddb);
    }
}
```

在上例中，我为每个值占位符（由问号表示）绑定了值。只要绑定值的顺序与查询字符串中占位符的顺序一致，查询就可以根据需要设置任意数量的绑定值。

### 使用子查询进行更新

给列赋值的另一种方法是使用子查询来获取所需值并赋值给指定列。该子查询必须只返回一个值；否则会收到错误。也可以使用一个计算表达式并返回结果的函数。该表达式还可以包含另一个查询或某种计算。让我们看一个子查询示例：

```sql
UPDATE CityTemperature
SET Scale = (Select Scale from TemperatureScales where Country = 'Canada')
where Country = 'Canada'
```

### 使用连接进行记录更新

你也可以使用连接（`JOIN`）从另一个表中获取数据。在下面的例子中，我使用了`INNER JOIN`，但也可以使用`OUTER JOIN`或`CROSS JOIN`。不过，大多数情况下使用`INNER JOIN`。

```sql
UPDATE CityTemperature
SET TemperatureScale = 'C'
FROM CityTemperature ct
    INNER JOIN TemperatureScales s ON ct.id = s.id
WHERE ct.Country = 'Canada'
```

### 在`FROM`子句中使用子查询进行更新

`UPDATE`操作的另一种选择是在`WHERE`子句中使用子查询，如下例所示。这里，子查询被命名为`tmpSelect`，引用的正是这个内层查询（或子查询）中的`temperature`列。

只要在绑定列时保持占位符的顺序，你就可以在 Swift 中使用此查询。

```sql
UPDATE CityTemperature
SET Temperature = tmpSelect.Temperature
FROM (SELECT id, Temperature from TempReadings where id = CityTemperature.Id and City = 'Montreal') as tmpSelect
```

### 冲突时的更新操作

SQLite 提供了五种特殊的操作来处理 SQL 事务期间的冲突，分别是`ROLLBACK`、`IGNORE`、`FAIL`、`ABORT`和`REPLACE`。这些并非 SQL 标准的一部分，而是通过 SQLite API 提供的。这些特殊语句使得 SQL 脚本能够在更新数据时，在列级别处理冲突。它们处理与主键、唯一键、非空列及其值相关的约束。

对于`UPDATE`命令，与`INSERT`命令类似，`ON CONFLICT`会被替换为`OR`后跟上述关键字之一。我们将在以下部分中查看这些命令。

#### 更新或回滚记录

当使用`ROLLBACK`时，如果要更新的记录违反了约束（例如主键），`ROLLBACK`选项可以帮助你优雅地处理错误，并通过回滚事务继续执行。如果查询正在更新多条记录，`ROLLBACK`的行为将与`ABORT`操作类似。

```sql
UPDATE OR ROLLBACK BooksToRead
SET Title = 'To Kill a Mockingbird'
    , Author = 'Harper Lee'
WHERE id = 2
```

#### 更新或中止记录



