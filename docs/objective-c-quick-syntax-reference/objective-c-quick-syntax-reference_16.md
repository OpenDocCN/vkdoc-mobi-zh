# 11. While 循环

## 摘要

与 `for` 循环类似，当你想多次重复执行相似类型的任务时，会用到 `while` 循环。`While` 循环用于当你想要重复执行一行代码多次，直到满足某个条件时。下面是一个 `while` 循环示例，它将向控制台窗口写入 10 次：

## While 循环详解

与 `for` 循环类似，当你想多次重复执行相似类型的任务时，会用到 `while` 循环。`While` 循环用于当你想要重复执行一行代码多次，直到满足某个条件时。下面是一个 `while` 循环示例，它将向控制台窗口写入 10 次：

```
int i = 0;
while (i < 10) {
    NSLog(@"i = %i", i);
    i++;
}
```

这个 `while` 循环将产生如下输出：

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

上面的循环与第 10 章的 `for` 循环功能相同，但请注意循环的各个部分位于不同的位置。你首先会注意到的是，在 `while` 循环中需要有一个计数器变量供其使用。你不能像之前那样直接将计数器变量放在循环体内部。所以你需要在使用 `while` 循环之前单独用一行代码来声明并赋值计数器变量。

```
int i = 0;
```

然后是 `while` 循环本身，它以 `while` 关键字开始。在 `while` 关键字后面的括号内是结束条件 `(i < 10)`。这意味着只要 `i` 的值小于 10，循环就会继续。

最后，有一个由花括号定义的代码块。包含在这些花括号中的代码将在每次循环时执行。你有一行代码 `NSLog(@"i = %i", i);` 用于写入日志。你还需要在此代码块中递增计数器变量：`i++;`。

**注意**

记住在此处递增计数器变量非常重要。如果你不这样做，那么 `i` 将永远不会超过 10。循环将永远不会结束，这实际上会导致你的程序挂起，直到用户终止它。



### While 循环与数组

现在，让我们重复第 10 章中的示例，该示例使用循环格式化数组中的数字列表。这是你在第 10 章中用过的数组和其他对象：

```
NSArray *list = @[@-2.0, @-1.0, @0.0, @1.0, @2.0];
NSMutableString *report = [[NSMutableString alloc] init];
NSNumberFormatter *formatter = [[NSNumberFormatter alloc] init];
formatter.numberStyle = NSNumberFormatterSpellOutStyle;
```

你将执行与第 10 章相同的操作，但将改用 `while` 循环实现。

```
int i = 0;
while(i < list.count) {
    NSNumber *num = [list objectAtIndex:i];
    NSString *spelledOutNum = [formatter stringFromNumber:num];
    [report appendString:spelledOutNum];
    [report appendString:@", "];
    i++;
}
```

上述操作是在遍历列表中的每个对象，并获取列表中与当前 `i` 值对应的索引所关联的数字引用。

接着，你使用数字格式化器获取数字的拼写字符串版本。最后，将这个拼写字符串值附加到可变字符串的末尾。以下是 `report` 的值：

```
report = minus two, minus one, zero, one, two,
```

