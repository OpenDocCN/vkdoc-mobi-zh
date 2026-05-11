# 使用 `break` 和 `continue`

在 Objective-C 中，`break` 和 `continue` 是用于循环（`for`、`while`、`do-while`）中的控制流语句，用于改变默认的执行流程。它们提供了操作循环的灵活性和控制能力，对于处理特定条件或提前退出循环至关重要。

## 1. 使用 `break`

`break` 语句终止当前循环或 switch 语句，并将控制权转移到终止语句之后的语句。

**示例 - 在 `for` 循环中使用 `break`：**

```objectivec
for (int i = 1; i <= 10; i++) {
    NSLog(@"%d", i);
    if (i == 5) {
        break; // 当 i 等于 5 时退出循环
    }
}
```

在此示例中，循环打印数字从 1 到 5。当 `i` 等于 5 时，遇到 `break` 语句，循环立即终止。



### 示例——在 `while` 循环中使用 `break`

```objectivec
int num = 1;
while (num <= 10) {
    NSLog(@"%d", num);
    num++;
    if (num == 6) {
        break; // 当 num 等于 6 时退出循环
    }
}
```

此循环会打印数字 1 到 5，并在 `num` 等于 6 时因 `break` 语句而退出。

## 使用 `continue`

`continue` 语句会跳过当前循环迭代的剩余部分，并继续执行下一次迭代。

### 示例——在 `for` 循环中使用 `continue`

```objectivec
for (int i = 1; i <= 5; i++) {
    if (i == 3) {
        continue; // 当 i 等于 3 时跳过该次迭代
    }
    NSLog(@"%d", i);
}
```

在此示例中，循环因 `continue` 语句而跳过打印数字 3，并继续执行下一次迭代。

### 示例——在 `while` 循环中使用 `continue`

```objectivec
int num = 0;
while (num < 5) {
    num++;
    if (num % 2 == 0) {
        continue; // 跳过偶数
    }
    NSLog(@"%d", num);
}
```

此循环打印奇数（1, 3, 5），因为 `continue` 语句跳过了偶数（满足 `num % 2 == 0` 条件）。

