# 第 3 章：控制结构

条件语句（`if`、`else`、`switch`）和循环（`for`、`while`、`do-while`）是 Objective-C 中基本的控制结构。它们允许你根据条件控制程序的执行流程，并多次重复执行代码块。让我们通过示例逐一探讨这些结构。

## 1. 条件语句

### `if`、`else` 语句

`if` 语句在指定条件为真时执行一段代码。可选地，可以使用 `else` 语句在条件为假时执行另一段代码。

**示例：**

```objectivec
int age = 30;
if (age >= 18) {
    NSLog(@"You are an adult.");
} else {
    NSLog(@"You are not yet an adult.");
}
```

### `else if` 语句

你可以使用 `else if` 设置多个条件，以顺序检查多个条件。

**示例：**

```objectivec
int score = 85;
if (score >= 90) {
    NSLog(@"Grade: A");
} else if (score >= 80) {
    NSLog(@"Grade: B");
} else if (score >= 70) {
    NSLog(@"Grade: C");
} else {
    NSLog(@"Grade: D");
}
```

### `switch` 语句

`switch` 语句计算一个表达式，并根据匹配的 case 标签执行相应的代码块。

**示例：**

```objectivec
int day = 2;
switch (day) {
    case 1:
        NSLog(@"Monday");
        break;
    case 2:
        NSLog(@"Tuesday");
        break;
    case 3:
        NSLog(@"Wednesday");
        break;
    default:
        NSLog(@"Other day");
        break;
}
```

`break` 语句用于终止 `switch` 语句，并将控制权转移到 switch 块之后的语句。

## 2. 循环

### `for` 循环

`for` 循环将代码块重复执行指定的次数。

**示例：**

```objectivec
for (int i = 1; i <= 5; i++) {
    NSLog(@"%d", i);
}
```

该循环将 `i` 初始化为 1，只要 `i` 小于或等于 5 就执行循环体，每次迭代后 `i` 增加 1。

### `while` 循环

`while` 循环在指定条件为真时重复执行代码块。

**示例：**

```objectivec
int count = 1;
while (count <= 5) {
    NSLog(@"%d", count);
    count++;
}
```

只要 `count` 小于或等于 5，循环就会继续执行，每次迭代后 `count` 增加 1。

### `do-while` 循环

`do-while` 循环与 `while` 循环类似，但它保证代码块在检查条件之前至少执行一次。

**示例：**

```objectivec
int num = 1;
do {
    NSLog(@"%d", num);
    num++;
} while (num <= 5);
```

该循环首先执行代码块，然后检查条件（`num <= 5`），只要条件为真就继续执行。

条件语句（`if`、`else`、`switch`）允许你在 Objective-C 程序中根据条件做出决策。循环（`for`、`while`、`do-while`）使你能够根据指定条件多次重复执行代码块。理解和掌握这些控制结构对于编写高效且功能完善的 Objective-C 代码至关重要，它们能让你控制程序流程并有效处理重复任务。

