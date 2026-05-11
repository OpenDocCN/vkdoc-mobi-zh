# 20. 块（Blocks）

## 摘要

块是一种定义代码块的方式，你可以在以后使用它。块与方法或函数有很多相似之处，它们都可以接受参数并返回值。有时人们将块称为匿名函数，因为它们是不附着于任何实体的函数。

## 块的定义

块是一种定义代码块的方式，你可以在以后使用它。块与方法或函数有很多相似之处，它们都可以接受参数并返回值。有时人们将块称为匿名函数，因为它们是不附着于任何实体的函数。

块与众不同的一个特点是，它们与程序的其余部分编写在相同的作用域内，因此你无需类定义即可添加块。块还具有其他一些非常有用的特性。虽然块不需要附着于对象，但块可以被当作对象来处理。这意味着你可以编写一组块，然后将它们存储在数据集合中。

块还会复制声明块时作用域内所有变量的值。这一特性使得块能够捕获状态，并在将来使用该状态，即使在块执行时原始变量已经超出作用域。

### 定义块

举个例子，我们来编写一个块，它接受一个浮点数作为参数，并返回其平方后的浮点数结果。将这个块命名为`squareThis`。首先需要声明块。这类似于声明数据类型或对象，但有一些差异，使你可以利用块类似函数的行为。

```
float (^squareThis)(float);
```

块声明以`float`返回类型开头。这意味着当你使用这个块时，将得到一个浮点数返回。

块声明的下一部分是块名`squareThis`，前面是脱字符（`^`）。整个名称括在圆括号内。

最后，括号内是一组参数类型。如果有多个参数类型，则列表必须用逗号分隔。代码行以分号结束。

### 赋值块

你可以使用赋值运算符（`=`）将代码块赋值给刚刚声明的块。当你将块代码赋值给块变量时，需要使用脱字符并声明变量名。还需要包含用花括号括起来的代码作用域。

```
squareThis = ^(float x){
    return x * x;
};
```

这个块将接受提供的数字，将数字乘以自身，然后返回结果。

### 使用块

当你准备好执行代码时，可以像调用函数一样调用块。以下是使用`squareThis`的方法：

```
float result = squareThis(4);
NSLog(@"result = %f", result);
```

这将在控制台日志中打印出以下输出：

```
result = 16.000000
```

### 复制作用域内的变量

块会复制声明块时当前作用域内每个变量的值。这意味着块可以保存周围变量的状态，以便在以后执行块时使用，无论变量是否仍在作用域内。

下面是一个名为`multiplyThese`的块的示例，它接受两个数字并将它们相乘，返回一个浮点数结果。这个块需要两个参数，我们将同时定义并赋值该块。请注意，在`multiplyThese`块附近还定义了一个字符串。

```
NSString *title = @"乘法块执行";

float (^multiplyThese)(float, float) = ^(float x, float y){
    NSLog(title);
    return x * y;
};
```

在返回结果之前，`multiplyThese`块会打印出它从上下文中捕获的字符串值。要使用此块，可以这样做：

```
NSLog(@"multiplyThese(3,4) = %f", multiplyThese(3,4));
```

这将产生以下输出：

```
乘法块执行
multiplyThese(3,4) = 12.000000
```



### 作为属性的 Block

尽管 Block 不像类那样需要实体，但你仍然可以使用 Block 来增强对象的灵活性。通过将 Block 作为属性添加到对象中，你可以提供一个接口，让客户端能够将自定义行为注入到你的对象中。

#### Block 的前向声明

由于 Block 的处理方式与对象类似，因此你可以将 Block 用作属性。为此，你需要从类接口（无论是公开接口还是私有类扩展）开始。下面演示了如何将`customReport` Block 添加到最初在第 16 章中定义的`Project`类中：

```
#import <Foundation/Foundation.h>

@interface Project : NSObject{
    @private NSString *log1;
    @protected NSString *log2;
    @public NSString *log3;
}

@property(strong) NSString *name;
@property (copy) void (^makeCustomReport)(NSString *title);

-(void)generateReport;
-(void)generateReportAndAddThisString:(NSString *)string
                   andThenAddThisDate:(NSDate *)date;
+(void)printTimeStamp;

@end
```

你需要使用`copy`属性描述符，因为你需要 Block 及其所有作用域变量被正确拷贝和保留。作为类的作者，接下来需要确定何时执行这个 Block，同时要记住，你永远无法提前预知 Block 中会包含什么代码。

#### 在类中使用 Block

对于这个例子来说，`generateReport`方法看起来是一个适合执行 Block 的位置。你需要进入`Project`类的实现部分，找到`generateReport`方法并添加这个 Block 调用。

```
#import "Project.h"

@implementation Project

-(void)generateReport{
    NSLog(@"This is a report!");
    self.makeCustomReport(@"Custom Project Report Title");
}

@end
```

如果观察上面的加粗代码，你会发现使用参数 Block 与使用其他 Block 类似，唯一的区别是这里使用了`self`关键字来引用该 Block。

#### 分配 Block

这种模式真正强大之处在于，即使你不确切知道某个行为是什么，或者该行为会随时间变化，你仍然可以设立一种方式来执行该行为。

要使它生效，客户端需要为 Block 赋值，并为你实际定义该行为。你可以在示例的主函数中完成此操作。

```
Project *p =[[Project alloc]init];
p.makeCustomReport = ^(NSString* title){
    NSLog(@"%@", title);
    NSLog(@"This is a custom report requested by the author");
    NSLog(@"Say This");
    NSLog(@"Say That");
    NSLog(@"Say The Other Thing");
};  
[p generateReport];
```

你需要为示例发送`generateReport`消息；发送后，你将得到以下输出：

```
This is a report!
Custom Project Report Title
This is a custom report requested by the author
Say This
Say That
Say The Other Thing
```

