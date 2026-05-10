# 使用 `JOIN` 子句

添加 `JOIN` 子句会在两个表实例之间创建一个临时关系，该关系仅在 SQL 查询执行期间存在。它们用于从两个表中选择字段并返回混合结果。`JOIN` 允许其他子句（如 `WHERE` 或 `ORDER BY`）引用任一表或两个表中的字段。例如，可以选择相关的*公司*记录位于特定州的*联系人*记录，并且结果联系人列表可以按公司名称排序。`JOIN` 子句包含一个表名，该表应与 `FROM` 表建立关系，并使用一个表达式来表述用于形成临时关系的条件。完整的公式模式如下所示：

```
SELECT  FROM  JOIN  ON 
```

以下示例连接*联系人*和*公司*表，以选择每个联系人的名字及其关联的公司名称，条件是在*联系人*（别名为 `con`）中的 *Contact Company ID* 字段等于*公司*（别名为 `com`）中的 *Record ID* 字段。它为表分配了别名，并在字段列表和连接子句中将其用作前缀。

```
SELECT
con."Contact Name First",
com."Company Name"
FROM Contact AS con
JOIN Company AS com ON
con."Contact Company ID" = com."Record ID"
```

