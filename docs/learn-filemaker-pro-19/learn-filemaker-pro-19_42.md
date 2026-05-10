# Choose

`Choose`函数求值单个测试条件以生成一个整数，并使用该数字从一个或多个结果值列表中进行选择。*测试*参数必须包含一个结果为整数的文本或数值表达式。可以包含任意数量的*结果*参数，并将根据测试生成的数字返回对应的结果。结果可以是字面量值，也可以是求值为任意数据结果的表达式。索引是*从零开始*的，因此如果测试求值为 0，则返回列出的第一个结果；测试值为 1 时返回第二个结果，依此类推。

```
Choose ( test ; result0 ; result1 ; result2 ; result3 )
```

在此示例中，`Status`字段包含 0 到 4 的数字，`Choose`函数用于将其转换为文本状态。

```
Choose ( Status ; "Low" ; "Guarded" ; "Elevated" ; "High" ; "Severe" )
```

此示例使用随机数从四个姓名中选出一个。

```
Choose ( Random * 4 ; "Jim Thomas" ; "Shannon Miller" ; "Charlene Smith" ; "Karen Camacho" )
```

