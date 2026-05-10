# 第 7 章 ■ 更新记录

## SQLite 中的更新选项

`ABORT` 选项会在遇到约束问题时（例如空记录或更新值的数据类型不正确）中止当前事务操作。SQLite 操作将回滚当前操作，但保留之前所有事务不变。

```
UPDATE OR ABORT BooksToRead
SET Title = 'To Kill a Mockingbird'
, Author = 'Harper Lee'
WHERE id = 2
```

#### 更新或替换记录

当查询遇到存在约束问题的行时，如果使用了 `REPLACE` 选项，查询将删除该约束行并用新行替换，从而消除约束。如果存在任何 `DELETE` 触发器，这些触发器也会被触发。

```
UPDATE OR REPLACE BooksToRead
SET Title = 'To Kill a Mockingbird'
, Author = 'Harper Lee'
WHERE id = 2
```

#### 更新或失败记录

如果使用 `FAIL` 选项，当事务遇到约束问题并抛出 `SQLITE_CONSTRAINT` 时，之前的事务（如果有）会保留，但当前失败的事务以及后续事务将不会被查询处理。操作会在出错的事务处停止。

```
UPDATE OR FAIL BooksToRead
SET Title = 'To Kill a Mockingbird'
, Author = 'Harper Lee'
WHERE id = 2
```

#### 更新或忽略记录

当使用 `IGNORE` 选项时，有问题的行会被跳过，其余行（如果有）将继续被处理。

```
UPDATE OR IGNORE BooksToRead
SET Title = 'To Kill a Mockingbird'
, Author = 'Harper Lee'
WHERE id = 2
```

## Swift 中的 SQLite 更新操作示例

为了演示 `UPDATE` 查询，我们将为之前章节中使用的极简 iOS 应用添加更新功能。在本部分，我们将向 `CrudOp` 自定义类中添加 `updateRecords` 方法，用于更新数据库中的记录。

首先，在 Xcode 中打开 Crud iOS 项目并找到 `CrudOp.h` 头文件。按如下方式添加 `updateRecords` 方法：

```
func updateRecords(){

    let sql:String = "Update data set coltext=? where coltext=?"

    if(sqlite3_open(dbPath.path!, &db)==SQLITE_OK){

        if(sqlite3_prepare_v2(db, sql, -1, &sqlStatement, nil) == SQLITE_OK)
        {
            sqlite3_bind_text(stmt, 1, txt, -1, SQLITE_TRANSIENT);
            sqlite3_bind_text(stmt, 2, utxt, -1, SQLITE_TRANSIENT);
        }
    }

    sqlite3_step(stmt);
    sqlite3_finalize(stmt);
    sqlite3_close(cruddb);
}
```

如您所见，代码模式与其他 SQLite 操作非常相似。在创建 `sqlite3_stmt` 和本地 `sqlite3` 数据库对象后，我们将更新查询创建为 `const char`。该查询也可以是 `NSString` 对象，在这种情况下，您无需指定 `UTF8String` 编码即可将字符串转换为通用字符集。接下来，我们建立与数据库引擎的连接，并使用 `cruddatabase` `NSString` 变量打开数据库。然后，我们使用 `UTF8string` 属性和 `sqlite3` 对象对其进行编码。

连接建立后，我们可以使用 `sqlite3_prepare_v2` 函数执行查询，并为 SET 和 WHERE 子句绑定参数变量。绑定函数必须与列和参数值的数据类型匹配，否则您将收到错误，除非将值转换为正确的类型。

`sqlite3_step` 函数将执行我们之前准备好的语句，并最终使用 `sqlite3_finalize` 完成操作，然后使用 `sqlite3_close` 关闭连接。这是 SQLite 中更新查询的基本设置。使用此模式，您可以执行任何类型的更新查询。

与其他 CRUD 操作一样，我们将通过故事板中 `ViewController` 的 `kcbViewController` 自定义类的 `segButton` `IBAction` 来调用该方法。

## 向酒庄应用添加更新功能

酒庄应用的下一项功能是能够更新现有记录。我们需要修改 `wineyDAO` 类并添加两个函数：一个用于葡萄酒，另一个用于酒庄。接下来我们将介绍这些内容。

### 修改 WineryDAO 控制器



