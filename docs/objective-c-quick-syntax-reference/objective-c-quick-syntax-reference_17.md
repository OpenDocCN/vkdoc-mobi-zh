# 12. Do While 循环

**摘要**

`Do while` 循环与 `for` 循环和 `while` 循环的用途相同。其语法有所不同，`do while` 循环的显著特点是代码块至少会执行一次。这是因为结束条件直到循环末尾才会被评估。以下是使用 `do while` 循环计数到 10 的代码示例：

## Do While 循环的定义

`Do while` 循环与 `for` 循环和 `while` 循环的用途相同。其语法有所不同，`do while` 循环的显著特点是代码块至少会执行一次。这是因为结束条件直到循环末尾才会被评估。以下是使用 `do while` 循环计数到 10 的代码示例：

```
int i = 0;
do{
    NSLog(@"i = %i", i);
    i++;
}while (i <10);
```

这个 `do while` 循环将输出以下结果：

```
i = 0
i = 1
i = 2
i = 3
i = 4
i = 5
i = 6
i = 7
i = 8
i = 9
```

上述循环与第 10 章中的 `for` 循环以及第 11 章中的 `while` 循环功能相同。`do while` 循环的规范与 `while` 循环类似，但它们位于不同的代码行中。

与 `while` 循环一样，你需要准备一个计数器变量。

```
int i = 0;
```

然后是以 `do` 关键字开头的 `do while` 循环本身。紧跟在 `do` 关键字之后的是由花括号定义的代码块。每次循环时，花括号内的代码都会执行。这里有一行代码 `NSLog(@"i = %i", i);` 用于写入日志，同时你还在该代码块中递增计数器变量 `i++;`。

条件 `(i < 10)` 位于代码块之后的 `while` 关键字之后。这意味着只要 `i` 的值小于 10，循环就会继续。

> **注意：** 务必记得在此处递增计数器变量。如果不这样做，`i` 将永远不会超过 10，循环将永远不会结束，这会导致程序挂起，直到用户终止它。

### Do While 循环与数组

现在，让我们重复第 10 章和第 11 章中的示例，该示例使用循环格式化数组中的数字列表。这是你在第 10 章和第 11 章中用过的数组和其他对象：

```
NSArray *list = @[@-2.0, @-1.0, @0.0, @1.0, @2.0];
NSMutableString *report = [[NSMutableString alloc] init];
NSNumberFormatter *formatter = [[NSNumberFormatter alloc] init];
formatter.numberStyle = NSNumberFormatterSpellOutStyle;
```

你将改用 `do while` 循环实现。

```
int i = 0;
do {
    NSNumber *num = [list objectAtIndex:i];
    NSString *spelledOutNum = [formatter stringFromNumber:num];
    [report appendString:spelledOutNum];
    [report appendString:@", "];
    i++;
}while(i < list.count);

NSLog(@"report = %@", report);
```

最终你会得到一个可变字符串，并将其输出到控制台日志中。输出结果如下：

```
report = minus two, minus one, zero, one, two,
```

