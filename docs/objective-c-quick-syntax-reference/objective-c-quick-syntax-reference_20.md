# Switch 语句

Switch 语句用于根据整数值执行代码。要使 switch 语句工作，你需要定义一个层级变量，然后为预期的层级变量的每个可能值编写一个代码块。

## Switch 语句的定义

Switch 语句用于根据整数值执行代码。要使 switch 语句工作，你需要定义一个层级变量，然后为预期的层级变量的每个可能值编写一个代码块。

在本章中，假设你正在编写代码来帮助进行一些几何计算。你需要处理不同的形状，并希望计算每个形状的面积。你可以使用一个 `NSInteger` 类型的变量（如 `shape`）来跟踪你正在处理的形状类型。

```
NSInteger shape = 0;
```

`NSInteger shape` 的每个值都对应一种形状类型。零可以是正方形，一可以是平行四边形，二可以是圆形。像 `shape` 这样的变量被称为层级变量，因为它们代表了可能的层级。

为了本示例的目的，你还需要一个变量来存储任何计算的结果，这就是为什么你有一个名为 `area` 的浮点数变量。

```
float area;
```

### switch 关键字

现在，让我们来介绍 switch 语句本身。要开始一个 switch 语句，你需要 `switch` 关键字，后面跟括号中的层级变量。同时，你应该使用花括号为 switch 语句创建一个代码块。

```
switch (shape) {

}
```

### case 关键字

接下来，你可以定义与层级变量可能取到的每个值相关联的代码块。你使用 `case` 关键字将每个可能的值与一个代码块关联起来。

```
switch (shape) {
        case 0:{
            float length = 3;
            area = length * length;
            NSLog(@"Square area is %f", area);
            break;
        }
}
```

你上面看到的是 `case` 关键字后跟你要测试的值，这里是 `0`。然后是冒号，接着是用花括号定义的代码块，当 `shape` 的值为 `0` 时，该代码块将被执行。

### break 关键字

在上面代码块的末尾，你可以看到 `break` 关键字。这个关键字会将程序控制权返回给主程序，并跳出 case 语句。如果没有这个语句，那么一旦第一个 case 为真，后面的每一行代码都会被执行（switch 语句在找到真值后，会停止对层级变量的求值）。

### 完整的 Switch 语句

以下是包含多个 case 语句的 switch 语句样子：

```
switch (shape) {
    case 0:{
        float length = 3;
        area = length * length;
        NSLog(@"Square area is %f", area);
        break;
    }
    case 1:{
        float base = 16;
        float height = 24;
        area = base * height;
        NSLog(@"Parallelogram area is %f", area);
        break;
    }
    default:{
        area = -999;
        NSLog(@"No Shape Specified");
        break;
    }
}
```

### 默认 case

仔细观察上面的代码，你可以看到有一个 `default` 关键字。这个关键字用于定义一个默认 case，这是一种定义代码块的方式，当其他条件都不满足时，该代码块将被执行。因此，如果 `shape` 的值恰好是 6 并且没有定义相应的代码块，你可以确保至少默认 case 中包含的代码会被执行。

如果你运行本章的代码，在控制台日志中会看到以下内容：

```
Square area is 9.000000
```

