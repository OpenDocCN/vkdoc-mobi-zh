# Case

`Case`函数求值一个或多个表达式，并返回第一个结果为 true 的表达式所对应的结果。如果没有表达式为 true，则可以包含一个可选的默认结果。该函数产生的条件结果类似于嵌套的`if-then`函数，但处理多条语句时更加简洁。该函数至少需要包含一个*测试-结果对*作为参数，其中仅当*测试*部分为 true 时，*结果*才会被求值并返回。*测试*参数必须包含一个结果为布尔值的文本或数值表达式。*结果*参数可以是字面量结果，也可以是生成任意结果的表达式。

```
Case ( test ; result )
```

可以添加任意数量的附加测试条件，并且这些条件将按顺序依次求值，直到某个条件求值为 true，并返回对应的结果。

```
Case ( test ; result ; test2 ; result2 )
Case ( test ; result ; test2 ; result2 ; test3 ; result3)
```

可以在末尾包含一个未经测试的最终结果，当之前的测试条件均未产生 true 结果时，该结果将作为返回值。如果没有提供默认值，那么当所有测试均为 false 时，结果将为空。在以下示例中，如果`test`和`test2`条件均为 false，则结果将为`defaultResult`语句生成的任何内容。

```
Case ( test ; result ; test2 ; result2 ; defaultResult )
```

随着这些语句变得冗长，可以考虑添加制表符和回车符来垂直重新格式化它们，将每一对放在单独的行上，使语句更易于阅读和遵循逻辑执行顺序。

```
Case (
test ; result ;
test2 ; result2 ;
test3 ; result3 ;
test4 ; result4 ;
defaultResult
)
```

以下示例构建一个句子，并使用`Case`在`Qty`字段中的值大于 1 时，为单词 “widget” 添加可选的复数形式。

```
"Enclosed find " & Qty & " widget" & Case ( Qty > 1 ; "s" ) & " for inspection."
// result examples (varying by Qty) =
//    Enclosed find 1 widget for inspection.
//    Enclosed find 8 widgets for inspection.
```

在此示例中，名为`Elapsed`的字段包含发票逾期的天数，并生成四种不同文本结果之一以指示其状态。如果值为 32，则结果将为 “Past Due”，因为前两个测试为 false，而第三个为 true。类似地，值为 64 将导致 “Delinquent”，而值为 20 将获得默认结果 “On Time”。

```
Case (
Elapsed > 90 ; "Severe" ;
Elapsed > 60 ; "Delinquent"
Elapsed > 30 ; "Past Due"
"On Time"
)
```

注意：FileMaker 确实包含内置的`If`函数，但通常为了`Case`的优越性而弃用该函数。

