# 第 5 章：插入记录

### `createOrOpenDatabase` 函数

`createOrOpenDatabase` 函数在应用加载时由`AppDelegate`函数调用，以确保数据库存在并执行模式脚本。我们可以通过“文件存在”检查来封装此操作，以避免在每次加载应用时都毫无必要地打开并执行模式脚本。

```swift
func createOrOpenDatabase()->Enums.SQLiteStatusCode{
    return Enums.SQLiteStatusCode(rawValue: sqlite3_open(dbPath.absoluteString!.cString (using: String.Encoding.utf8)!, &db))!
}
```

### `insertWineRecord` 函数

此函数通过`insertRecordAction` IBAction 从`FirstViewController`调用。首先，我们定义一个插入查询并将其赋值给一个名为`sql`的常量。接着，我们打开所需的数据库，并在执行`sqlite3_prepare_v2`函数前确保它以`SQLITE_OK`状态码打开，该函数接受`sqlite`指针、`sql`查询和`sqlStatement`指针作为参数。

此函数的一个特殊之处在于`wine.image`，它将`NSData`转换为 blob 以插入数据库。要进行转换，我们需要从`NSData`中获取字节，然后提供字节的长度并将其转换为`Int32`。

在打开数据库并准备查询语句后，我们使用针对所需数据类型的适当绑定函数，将输入值绑定到列。我们使用`sqlite3_step`函数执行查询，然后使用`sqlite3_finalize`函数清理操作。剩下的就是使用`sqlite3_close`函数关闭数据库：

```swift
func insertWineRecord(_ wine:Wine)->Enums.SQLiteStatusCode{
    let sql:String = "INSERT INTO main.wine VALUES(?, ?, ?, ?)"
    if(sqlite3_open(dbPath.path!, &db)==SQLITE_OK){
        if(sqlite3_prepare_v2(db, sql.cString (using:String.Encoding.utf8)!, -1, &sqlStatement, nil)==SQLITE_OK){
            sqlite3_bind_value(sqlStatement, 1, nil)
            sqlite3_bind_text(sqlStatement, 2, wine.name.cString(using: String.Encoding.utf8)!, -1, SQLITE_TRANSIENT)
            sqlite3_bind_int(sqlStatement, 3, wine.rating)
            sqlite3_bind_int(sqlStatement, 4, wine.producer)
            sqlite3_bind_blob(sqlStatement, 5, wine.image.bytes,Int32(wine.image.count), SQLITE_TRANSIENT)
            sqlite3_step(sqlStatement)
            sqlite3_finalize(sqlStatement)
        }
    }else{
        print(String(cString: sqlite3_errmsg(db))!)
        return Enums.SQLiteStatusCode.error
    }
    sqlite3_close(db)
    return Enums.SQLiteStatusCode.ok
}
```

### `insertWineryRecord` 函数



