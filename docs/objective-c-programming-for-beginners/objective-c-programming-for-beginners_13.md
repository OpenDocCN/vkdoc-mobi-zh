# 递归函数

递归函数是直接或间接调用自身以解决问题的函数。它们适用于解决可以分解为更小、更相似的子问题的问题。理解递归函数对于处理涉及重复或嵌套结构的任务至关重要。

## 1. 递归函数的基础

递归函数通常包含两个主要部分：

- **基线条件**：函数停止递归调用自身的条件。它防止无限递归，并定义了可以直接解决的最小问题实例。
- **递归条件**：函数调用自身来处理更小或更简单问题实例的部分。

## 2. 递归函数示例

**示例 - 计算阶乘**

非负整数 `n` 的阶乘，记作 `n!`，是所有小于或等于 `n` 的正整数的乘积。

```
#import <Foundation/Foundation.h>

// 函数声明
int factorial(int n) {
    // 基线条件：0 的阶乘是 1
    if (n == 0) {
        return 1;
    }
    // 递归条件：n! = n * (n-1)!
    return n * factorial(n - 1);
}
```


```objectivec
return n * factorial(n - 1);
}
int main(int argc, const char * argv[]) {
    @autoreleasepool {
        int number = 5;
        int result = factorial(number);
        NSLog(@"Factorial of %d is %d", number, result); // 输出: Factorial of 5 is 120
    }
    return 0;
}
```

在此示例中，`factorial` 是一个递归函数，用于计算数字 `n` 的阶乘。基本情况（`n == 0`）返回 `1`，递归情况计算 `n * factorial(n - 1)`。

## 3. 重要注意事项

- **基本情况**：确保每个递归函数都有一个最终能终止递归的基本情况。
- **栈**：递归函数使用调用栈来存储中间结果和函数调用。如果管理不当，过多的递归可能导致栈溢出。
- 由于函数调用和栈管理的开销，对于某些问题，递归函数可能不如迭代解决方案高效。

## 4. 多路调用递归函数示例

### 示例 - 斐波那契数列

斐波那契数列是一系列数字，其中每个数字是前两个数字之和，通常以 `0` 和 `1` 开头。

```objectivec
#import <Foundation/Foundation.h>

// 函数声明
int fibonacci(int n) {
    // 基本情况
    if (n == 0) {
        return 0;
    }
    if (n == 1) {
        return 1;
    }
    // 递归情况：斐波那契(n) = 斐波那契(n-1) + 斐波那契(n-2)
    return fibonacci(n - 1) + fibonacci(n - 2);
}

int main(int argc, const char * argv[]) {
    @autoreleasepool {
        int number = 6;
        int result = fibonacci(number);
        NSLog(@"Fibonacci of %d is %d", number, result); // 输出: Fibonacci of 6 is 8
    }
    return 0;
}
```

在此示例中，`fibonacci` 是一个递归函数，用于计算第 n 个斐波那契数。它有两个基本情况（`n == 0` 和 `n == 1`）以及一个计算 `fibonacci(n - 1) + fibonacci(n - 2)` 的递归情况。

Objective-C 中的递归函数提供了一种优雅的方式，通过将问题分解为更小、更易于管理的子问题来解决它们。它们是强大的工具，但需要谨慎设计，以确保它们能正确终止并高效处理栈的使用。通过理解递归函数的原理和示例，你可以利用这一技术有效解决各种编程挑战。

