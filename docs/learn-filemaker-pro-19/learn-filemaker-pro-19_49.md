# 使用 `ORDER BY` 子句

可以添加 `ORDER BY` 子句来指定结果排序。

```
SELECT  FROM  ORDER BY 
```

以下示例返回按州排序的每个联系人的姓氏列表。

```
SELECT "Contact Name Last" FROM Contact ORDER BY "Contact Address State"
```

将 `ORDER BY` 与 `WHERE` 子句结合使用，以下示例将返回居住在城市名为‘Milford’的每个联系人的姓氏，并按州排序。

```
SELECT "Contact Name Last"
FROM Contact
WHERE "Contact Address City" = 'Milford'
ORDER BY "Contact Address State"
```

