# 第 4 章：函数与方法

函数是可重用的代码块，用于执行特定任务。

在 Objective-C 中，函数使用特定的语法定义，并且可以有输入参数和输出返回类型。理解如何定义和调用函数是有效组织和构建代码的基础。

## 1. 定义函数

在 Objective-C 中，函数使用以下语法定义：

```objectivec
returnType functionName(parameter1Type parameter1Name, parameter2Type parameter2Name, ...) {
    // 函数体
    // 语句
    return returnValue; // 可选的返回语句
}
```

- `returnType`：指定函数返回值的类型。如果函数不返回值，则使用 `void`。
- `functionName`：函数名称。
- `parameters`：函数接受的参数（输入）的可选列表，每个参数由数据类型和名称定义。
- `returnValue`：函数返回的可选值（如果 `returnType` 不是 `void`）。

### 示例——无参数和无返回值的函数

```objectivec
// 函数声明
void greet() {
    NSLog(@"Hello, World!");
}

// 函数调用
greet(); // 输出：Hello, World!
```

### 示例——带参数和返回值的函数

```objectivec
// 函数声明
int sum(int a, int b) {
    return a + b;
}

// 函数调用
int result = sum(3, 5); // result 的值为 8
NSLog(@"Sum: %d", result); // 输出：Sum: 8
```

## 2. 函数参数和返回类型

### 函数参数

参数允许你将值传递给函数进行处理。它们定义在函数名后面的括号内。

#### 示例——带参数的函数

```objectivec
// 函数声明
void greetUser(NSString *name) {
    NSLog(@"Hello, %@", name);
}

// 函数调用
greetUser(@"Alice"); // 输出：Hello, Alice
```

### 返回类型

返回类型指定函数返回值的类型。如果函数不返回值，则使用 `void`。

#### 示例——带返回类型的函数

```objectivec
// 函数声明
int square(int number) {
    return number * number;
}

// 函数调用
int result = square(5); // result 的值为 25
NSLog(@"Square: %d", result); // 输出：Square: 25
```

函数对于将代码组织成可管理的块、增强可重用性以及促进 Objective-C 程序中的模块化设计至关重要。通过定义带有适当参数和返回类型的函数，你可以封装逻辑和任务，使代码更易读、可维护且高效。理解如何定义函数、传递参数以及处理返回值，对于开发健壮且可扩展的 Objective-C 应用程序至关重要。

