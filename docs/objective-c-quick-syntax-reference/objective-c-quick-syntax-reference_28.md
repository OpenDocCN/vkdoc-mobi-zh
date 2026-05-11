# 协议

## 摘要

协议用于独立于类定义一组方法和属性。任何类都可以采纳协议，这意味着该类实现了协议中定义的属性和方法。实际上，协议定义了一个契约，类可以同意履行该契约。当一个类采纳协议时，你可以确信该类已经实现了协议中的属性和方法。

## 协议概述

协议用于独立于类定义一组方法和属性。任何类都可以采纳协议，这意味着该类实现了协议中定义的属性和方法。实际上，协议定义了一个契约，类可以同意履行该契约。当一个类采纳协议时，你可以确信该类已经实现了协议中的属性和方法。



### 定义协议

要使用协议，必须首先定义协议。使用 `@protocol` 关键字开始定义协议。你可以将其包含在与协议相关联的类所在的同一文件中，也可以将协议放在一个单独的头文件中。如果你想为上一章中编写的 `Task` 类定义一个协议，可以这样做：

```
#import <Foundation/Foundation.h>
@class Task;
@protocol TaskDelegate <NSObject>
@optional
-(void)thisTask:(Task *)task statusHasChangedToThis:(BOOL)done;
@end
@interface Task : NSObject
@end
```

协议名称跟在 `@protocol` 关键字后面，尖括号中的内容是要继承的协议。`NSObject` 是所有 `NSObject` 类必须遵循的协议。继承一个协议类似于继承一个类，但现在意味着采用你定义的协议的类，除了要实现继承协议中定义的方法之外，还要负责你定义的方法。

协议定义以 `@end` 关键字结束。位于 `@protocol` 和 `@end` 之间的所有方法和属性，都是类在采用该协议时需要实现的内容。

注意：上面需要用到 `@class` 关键字，是因为在下面定义 `Task` 类之前，你已经在协议中引用了它。`@class` 提供了一种在不使用接口的情况下引用类的方式。

#### 可选与必需的方法和属性

协议方法默认是必需的。不过，你可以将某些方法指定为可选的。可选方法用于功能存在但并非关键的情况。要标记方法为可选，请使用 `@optional` 关键字。在 `@optional` 关键字之后出现的每个属性和方法都将被视为可选。

使用 `@required` 关键字将方法和属性标记为必需。之后的所有方法和属性都将被视为必需。

#### 采用协议

你可以在类接口中父类后面的尖括号里包含协议名称，以此指示某个类将采用该协议。如果一个类要采用多个协议，则必须在尖括号内提供以逗号分隔的协议名称列表。

如果你想让 `Project` 采用 `TaskDelegate` 协议，可以像这样在 `Project` 接口中采用该协议：

```
#import <Foundation/Foundation.h>
#import "Task.h"
@interface Project : NSObject <TaskDelegate>
@property(strong) NSString *name;
@property(strong) NSMutableArray *listOfTasks;
-(void)generateReport;
@end
```

一旦采用 `TaskDelegate` 协议，就表示你同意实现 `TaskDelegate` 中的方法和属性。如果现在尝试构建项目，你会收到一条警告。接下来要做的就是实现 `TaskDelegate` 中定义的方法。

#### 实现协议方法

实现协议方法与实现其他方法完全相同。你需要在采用该协议的类的实现中实现这些协议方法。

对于这个例子，你需要在 `Project` 类的实现中添加这个方法：

```
#import "Project.h"
@implementation Project
-(void)thisTask:(Task *)task statusHasChangedToThis:(BOOL)done{
    NSLog(@"Task '%@' is now %@", task.name, done ? @"DONE" : @"IN PROGRESS");
}
@end
```

你还不能测试这段代码，因为你还需要添加代码，让 `Task` 对象能够使用这个协议。这属于下一章将要介绍的委托设计模式的一部分。

