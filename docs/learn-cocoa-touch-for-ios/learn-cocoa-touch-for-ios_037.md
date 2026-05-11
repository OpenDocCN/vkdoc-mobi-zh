# Objective-C 中的块

## 块是封装好的函数

对于我们的第一个块，不妨遵循传统方式，创建一个“Hello, World!”块。该块在执行时会在控制台打印“Hello, World!”，仅此而已。它既不接受参数，也不返回任何值，因此其返回类型为`void`，就像不返回值的函数或方法一样。创建该块的方法如下：

```objective-c
void (^helloBlock)(void) = ^ void (void){ NSLog(@"Hello, World!"); };
```

让我们分解一下。首先，我们看到了返回类型，在本例中是`void`。接着，在括号内，有一个脱字符（`^`）表示这是一个块，后面跟的是用于存储该块的临时变量名称，因此我们得到`(^helloBlock)`。最后，另一组括号中包含了该块接受参数的逗号分隔列表，这里没有参数，所以就是简单的`(void)`。表达式左侧的最终结果是一个名为`helloBlock`的新的临时变量，其中包含一个块。如果你曾使用过 C 语言中的函数指针，你会注意到语法几乎完全相同，区别在于函数使用星号（`*`），而块使用脱字符（`^`）。

这个表达式的右侧就是块本身。它以脱字符开始，接着是返回类型（此处为`void`），然后在括号中列出参数列表，这里同样是`(void)`。之后，位于花括号（`{`和`}`）之间的就是块的实际代码。请注意，这里有两个分号。一个结束块内的表达式（即`NSLog()`函数调用），另一个结束定义这个块的表达式。忘记其中一个或两个分号，是练习调试代码时最容易犯的错误之一。

为了节省空间，可以从右侧省略`void`返回类型和参数列表；之前的代码可以改写如下：

```objective-c
void (^helloBlock)(void) = ^{ NSLog(@"Hello, World!"); };
```

如你所见，这样简洁多了。回顾一下，这一行代码（以及之前的版本）创建了一个块，该块在执行时会调用`NSLog()`，然后将该块存储在一个名为`helloBlock`的变量中。那么，我们如何执行它呢？就像调用函数一样：

```objective-c
helloBlock();
```

调用`helloBlock()`将导致该块的代码立即执行。

有趣的是，在调用之前并不一定要将块存储在变量中。下面这行代码也能成功在控制台打印“Hello, World!”：

```objective-c
^{ NSLog(@"Hello, World!"); }();
```

这个特定代码示例的优点并不多，但它是块匿名性的一个有趣例子。

## 使用类型定义实现可读的块声明

前面的例子是块声明中最简单的情况。在实践中，块声明要复杂得多。考虑一个返回`BOOL`值并接受两个参数（类型分别为`id`和`NSError*`）的块。声明一个用于存储该块的变量`resultHandler`的方法如下：

```objective-c
BOOL (^resultHandler)(id result, NSError *error);
```

这还不算太糟，其可读性足以直接使用。然而，如果它还有第三个参数，且该参数本身又是一个块呢？这样一来情况就变得更复杂了：

```objective-c
BOOL (^resultHandler)(id result, NSError *error, NSString *(^helperBlock)(void));
```

如你所见，事情很快就变得难以掌控。为了解决这个问题，你可以使用`typedef`来定义常见的块类型，这是 C 语言中定义自定义类型的方法。之前展示的第一个`resultHandler`块可能是一种足够常见的块风格，你希望将它作为一个名为`ResultHandler`的独立类型来引用。这可以通过以下一行代码实现：

```objective-c
typedef BOOL (^ResultHandler)(id result, NSError *error);
```

有了我们新定义的类型，我们可以更简洁地声明这种块类型的变量：

```objective-c
ResultHandler resultHandler;
```



```objc
ResultHandler resultHandler = ^ BOOL (id result, NSError *error) {
    [result performSomeTask];
    return YES;
};
```

