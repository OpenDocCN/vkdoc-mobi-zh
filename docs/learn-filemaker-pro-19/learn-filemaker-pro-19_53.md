# 限制查询结果

`OFFSET` 和 `FETCH FIRST` 子句可以单独使用或联合使用，以控制查询返回的结果数量。

## 使用 `OFFSET` 子句

`OFFSET` 子句用于指定要从结果顶部排除的记录数量。此示例将排除前 20 条记录，并从第 21 条记录开始返回结果：

```
SELECT "Record ID" FROM Contact OFFSET 20 ROWS
```

## 使用 `FETCH FIRST` 子句

`FETCH FIRST` 子句限制返回的行数。此示例将仅返回前十条结果：

```
SELECT "Record ID" FROM Contact FETCH FIRST 10 ROWS ONLY
```

## 组合使用 `OFFSET` 和 `FETCH FIRST` 子句

`OFFSET` 和 `FETCH FIRST` 子句的组合可以从结果中获取特定的记录组。这允许以一系列小批次的方式提取结果子集，通常称为*分页结果*，其中每个查询一次返回一“页”结果。`OFFSET` 部分指示所需组的起始位置，`FETCH FIRST` 部分限制从该起始点访问的记录数量。以下代码展示了若干访问记录批次的示例，每次十批。

```
SELECT "Record ID" FROM Contact FETCH FIRST 10 ROWS ONLY
SELECT "Record ID" FROM Contact OFFSET 10 ROWS FETCH FIRST 10 ROWS ONLY
SELECT "Record ID" FROM Contact OFFSET 20 ROWS FETCH FIRST 10 ROWS ONLY
SELECT "Record ID" FROM Contact OFFSET 30 ROWS FETCH FIRST 10 ROWS ONLY
SELECT "Record ID" FROM Contact OFFSET 40 ROWS FETCH FIRST 10 ROWS ONLY
```

第一个语句返回记录 1 到 10，第二个返回记录 11 到 20，第三个返回记录 21 到 30，依此类推。使用此技术，界面元素可以允许用户一次一“页”地在结果组中来回点击。

