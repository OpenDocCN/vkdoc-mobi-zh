# 访问数据库模式

`ExecuteSQL` 函数能够访问两个提供数据库模式元信息的系统表：`FileMaker_Tables` 和 `FileMaker_Fields`。这些表可以在 `SELECT` 语句中像使用自定义表实例一样使用，以访问构成数据库结构的表和字段的信息。



### 选择 FileMaker_Tables

`FileMaker_Tables`表包含一个虚拟记录，针对关系图中定义的每个*表出现*，并包含以下信息字段：

- `TableName` – 表出现的名称
- `TableID` – 表出现的标识号
- `BaseTableName` – 表出现的基础表名称
- `BaseFileName` – 出现的基础表所在文件的名称
- `ModCount` – 自创建以来对表结构进行的修改次数

要选择数据库中每个表出现的所有五个字段，请在`ExecuteSQL`函数中使用以下查询公式：

```
"SELECT * FROM FileMaker_Tables"
// 结果 =
Company,1065101,Company,Learn FileMaker,20
Company | Contact,1065105,Contact,Learn FileMaker,24
Contact,1065102,Contact,Learn FileMaker,24
Contact | Company,1065106,Company,Learn FileMaker,20
Project,1065103,Project,Learn FileMaker,4
Project | Company,1065107,Company,Learn FileMaker,20
Sandbox,1065089,Sandbox,Learn FileMaker,134
```

此示例指定只返回`TableName`字段的结果：

```
"SELECT TableName FROM FileMaker_Tables"
// 结果 =
Company
Company | Contact
Contact
Contact | Company
Project
Project | Company
Sandbox
```

此示例使用`SELECT DISTINCT`和`BaseTableName`将结果限制为实际的表名称：

```
"SELECT DISTINCT BaseTableName FROM FileMaker_Tables"
// 结果 =
Company
Contact
Project
Sandbox
```

### 选择 FileMaker_Fields

`FileMaker_Fields`表包含一个虚拟记录，针对数据库中定义的每个*字段*，并提供以下可用元信息：

- `TableName` – 字段的表出现的名称
- `FieldName` – 字段的名称
- `FieldType` – 文件的 SQL 数据类型
- `FieldID` – 字段的标识号
- `FieldClass` – 字段的类别：`Normal`、`Summary`或`Calculated`
- `FieldReps` – 定义的最大重复次数
- `ModCount` – 自创建以来对字段进行的修改次数

此示例返回数据库中每个表每个字段的所有七个值：

```
"SELECT * FROM FileMaker_Fields"
```

由于此系统表包含基于*表出现*的字段，如果基础表有多个出现，结果将包含重复项。迭代过程（如`While`函数（第[13]章）、递归自定义函数（第[15]章）或循环脚本（第[25]章“使用重复语句迭代”））可以获取每个基础表的名称，然后逐步通过这些名称检索每个表的字段，从而避免重复。以下示例展示了一个简单的`While`语句，演示了如何做到这一点：

```
While (
[
baseTables =
ExecuteSQL ( "SELECT DISTINCT BaseTableName FROM FileMaker_Tables" ; "" ; "" ) ;
result = ""
] ;
baseTables ≠ "" ;
[
current.table = GetValue ( baseTables ; 1 ) ;
baseTables = RightValues ( baseTables ; ValueCount ( baseTables ) - 1 ) ;
current.fields =
ExecuteSQL (
"SELECT * FROM FileMaker_Fields WHERE TableName='" & current.table & "'" ; "" ; ""
) ;
result = result & current.fields
] ;
result
)
```

## 探索其他 SQL 功能

除了本章涵盖的内容之外，FileMaker 还包含额外的 SQL 功能。可以将许多函数嵌入到`SELECT`语句中以处理结果，其中许多函数提供的功能类似于 FileMaker 自己的函数。有多种命令可用于处理日期、时间和字符串。数值可以进行聚合或用于数学计算。可以嵌入条件操作，并且可以将许多运算符与字段值和 SQL 函数一起使用来处理结果。`Execute SQL`脚本步骤允许对来自外部数据库的外部 ODBC/JDBC 数据源进行更强大的操作。此外，FileMaker 数据库可以用作 ODBC/JDBC 数据源，并支持来自外部数据库的 SQL 查询。有关这些主题的更多信息，请访问[*www.*`claris.com`](http://www.claris.com)并搜索`FileMaker ODBC and JDBC Guide`或`FileMaker SQL Reference`文档。

## 总结

本章介绍了使用`ExecuteSQL`函数的基础知识，并提供了`SELECT`语句许多功能的示例。在下一章中，我们将开始探索布局设计，为用户提供访问数据结构界面的入口。

