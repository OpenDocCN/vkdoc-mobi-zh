# 10. For 循环

## 摘要

当你想多次重复执行相似类型的任务时，会用到循环。`For` 循环用于当你事先知道要重复执行相似代码行的次数时。下面是一个 `for` 循环示例，它将向控制台窗口写入 10 次：

## For 循环详解

当你想多次重复执行相似类型的任务时，会用到循环。`For` 循环用于当你事先知道要重复执行相似代码行的次数时。下面是一个 `for` 循环示例，它将向控制台窗口写入 10 次：

```
for (int i=0; i<10; i++) {
    NSLog(@"i = %i", i);
}
```

这个 `for` 循环将产生如下输出：

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

让我们更仔细地看一下这个循环的各个部分。首先是 `for` 关键字，它告诉编译器你正在编写一个 for 循环。

接着，在括号内有一系列代码行：`(int i=0; i<10; i++)`。这些代码行指定了起始条件（`int i=0;`）、结束条件（`i<10`）和增量指令（`i++`）。这意味着循环将重复 10 次，从 0 开始，只要变量 `i` 小于 10，每次循环执行时 `i` 就增加 1。

最后，有一个由花括号 `{ }` 定义的代码块。包含在这些花括号中的代码将在每次循环时执行。在上面的例子中，代码块只有一行代码：`NSLog(@"i = %i", i);`。请注意，变量 `i`（有时称为计数器变量）在作用域内，你可以在代码块中使用 `i`。

### For 循环与数组

通常情况下，你会使用 `for` 循环来遍历一个列表，并对列表中的每个对象执行某些操作。假设你有一个名为 `list` 的数组，其中包含五个范围从 -2.0 到 2.0 的 `NSNumber` 对象。

```
NSArray *list = @[@-2.0, @-1.0, @0.0, @1.0, @2.0];
```

假设你想构建一个字符串，其中包含 `list` 中的所有值，但要用单词拼写出来。例如，你想要一个字符串 `minus two, minus one, etc`。你需要使用 `NSNumberFormatter` 和一个 `NSMutableString`，这些都在关于数字和字符串的章节中介绍过。

为了设置好这一切，让我们在进入 `for` 循环之前，先获取数字格式化器、可变字符串和数组。

```
NSArray *list = @[@-2.0, @-1.0, @0.0, @1.0, @2.0];
NSMutableString *report = [[NSMutableString alloc] init];
NSNumberFormatter *formatter = [[NSNumberFormatter alloc] init];
formatter.numberStyle = NSNumberFormatterSpellOutStyle;
```

请注意，这里指定了 `NSNumberFormatterSpellOutStyle`，这样数字格式化器就能给出每个数字对象拼写出来的值。

由于你希望 `for` 循环遍历数组列表中的每个数字，你需要找出 `list` 中包含多少个对象。`NSArray` 对象有一个 `count` 属性，你可以用它来获取数组中包含的对象数量，并且你可以直接在 `for` 循环中使用这个值。

```
for (int i=0; i<list.count; i++) {
    NSNumber *num = [list objectAtIndex:i];
    NSString *spelledOutNum = [formatter stringFromNumber:num];
    [report appendString:spelledOutNum];
    [report appendString:@", "];
}
```

上面所做的就是遍历列表中的每个对象，并获取列表中与当前 `i` 值对应的索引位置上的数字引用。

接下来，使用数字格式化器获取该数字的拼写字符串版本。最后，将这个拼写的字符串值追加到可变字符串的末尾。以下是输出结果的样子：

```
report = minus two, minus one, zero, one, two,
```

