# 14. If 语句

**摘要**

当你希望根据条件的真假来决定是否执行代码时，可以使用 `if` 语句。为此，你需要评估一个使用关系运算符的表达式，以得到 `YES` 或 `NO` 的结果。如果表达式评估为真，则可以执行代码；否则可以忽略该代码。



## If 语句的定义

如果你想根据条件的真假来决定是否执行代码，就可以使用 if 语句。为此，你需要计算一个使用关系运算符的表达式，以得到“是”或“否”的结果。如果表达式计算结果为真，则执行代码；否则忽略该代码。

使用 if 语句需要 `if` 关键字、一个表达式以及一个代码块。

```
if(1 < 2){
    NSLog(@"That is true");
}
```

该语句的意思是：如果 1 小于 2，则执行代码，将字符串“That is true”打印到控制台日志中。

### else 关键字

你还可以使用 `else` 关键字定义一个备选操作。这提供了一种方式，让你可以根据所计算的表达式结果，执行两个操作中的一个。

```
if(1 < 2){
    NSLog(@"That is true");
}
else{
    NSLog(@"Not true");
}
```

### 嵌套 if-else

每个 if 语句都可以包含嵌套的 if 语句。这为你提供了测试多个条件的方式。一般来说，最好将嵌套 if 语句限制在最多三层。以下是嵌套 if 语句的样子：

```
if(1 > 2){
    NSLog(@"True");
}
else{
    if(3 > 4){
        NSLog(@"True");
    }
    else{
        NSLog(@"Not True");
    }
}
```

## if 语句与变量

通常，你会看到 if 语句与用于跟踪程序状态的变量一起使用。你可以在 if 语句的括号内将变量作为表达式的一部分，或者直接测试变量。

```
BOOL isTrue = 1 == 2;

if(isTrue){
    NSLog(@"isTrue = %@", isTrue ? @"YES" : @"NO");
    NSLog(@"That was a true statement.");
}
else{
    NSLog(@"isTrue = %@", isTrue ? @"YES" : @"NO");
    NSLog(@"That was not a true statement.");
}
```

在上面的代码中，你将表达式的结果赋值给了布尔变量 `isTrue`，然后稍后通过 if 语句对其进行测试。

如果你亲自测试这段代码，在控制台日志中会看到以下内容：

```
isTrue = NO
That was not a true statement.
```

