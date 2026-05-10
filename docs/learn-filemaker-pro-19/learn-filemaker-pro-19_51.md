# 使用 `GROUP BY` 子句

添加 `GROUP BY` 子句会基于一个或多个字段生成一个*聚合值*。这相当于原生 FileMaker 汇总字段的 SQL 版本（第 8 章）；两者都基于排序分组字段生成数据汇总。例如，如果*联系人*表有一个名为*State*的字段和另一个名为*Invoices*（包含客户发票金额总额）的字段，以下*不带* `GROUP BY` 子句的代码将返回每个联系人记录的*State*和*Invoice*金额列表，如下例所示：

```
SELECT State, Invoices FROM Contact ORDER BY State
// 结果 =
AK,1000
AK,500
AK,250
AZ,500
AZ,750
等其他
```

通过对*Invoices*字段使用 *Sum* 函数，并添加一个指定*State*字段的 `GROUP BY` 子句，以下示例将返回按州汇总的发票金额摘要。请注意，此示例中已移除了 `ORDER BY` 子句。这不是必需的，因为 `GROUP BY` 子句会对记录进行排序以便分组和汇总结果。

```
SELECT State, Sum ( Invoices ) FROM Contact GROUP BY State
// 结果 =
AK,1750
AZ,1250
CA,2500
```

## 添加 `HAVING` 子句

组合使用 `HAVING` 和 `GROUP BY` 子句允许语句定义将包含哪些*分组结果*，其作用类似于 `WHERE` 子句，但针对的是汇总结果。基于前面的示例，以下示例使用 `HAVING` 子句仅包含*发票汇总*大于某个金额的州的结果。这里，前一个示例中 `AZ` 的汇总条目已被移除，因为汇总总计 `1250` 低于 `HAVING` 子句中指定的阈值 `1500`。

```
SELECT State, Sum ( Invoices ) FROM Contact GROUP BY State HAVING Sum ( Invoices ) > 1500
-- 结果 =
AK,1750
CA,2500
```

