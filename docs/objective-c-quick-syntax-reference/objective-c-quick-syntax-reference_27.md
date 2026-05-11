# 22. 键值观察

概要

键值编码的应用之一是实现观察者模式。当你希望一个对象在另一个对象的状态发生变化时收到通知时，就会使用观察者模式。在 Objective-C 中，这种模式是通过键值观察来实现的。

## 键值观察定义

键值编码的应用之一是实现观察者模式。当你希望一个对象在另一个对象的状态发生变化时收到通知时，就会使用观察者模式。在 Objective-C 中，这种模式是通过键值观察来实现的。

要看到一个清晰的键值观察示例，你至少需要两个对象。一个对象将被观察，另一个对象则进行观察。在这个例子中，假设你有两种类型的对象：一个`Project`对象和一个`Task`对象。`Project`对象维护着一个`Task`对象的列表。当`Task`对象的状态发生变化时（例如，当任务被标记为完成时），项目对象需要得到通知。



## 项目与任务对象图

在实现键值观察之前，我们先了解这个对象图。为了便于说明，`Project` 类已做了简化，同时我添加了一个 `Task` 类的定义。对象图将在 `main.m` 文件中建立。

以下是 `Project` 类的接口：

```
#import <Foundation/Foundation.h>
#import "Task.h"

@interface Project : NSObject
@property(strong) NSString *name;
@property(strong) NSMutableArray *listOfTasks;
-(void)generateReport;
@end
```

以下是 `Project` 类的实现：

```
#import "Project.h"

@implementation Project
-(void)generateReport{
    NSLog(@"Report for %@ Project", self.name);
    [self.listOfTasks enumerateObjectsUsingBlock:^(id obj, NSUInteger idx, BOOL *stop) {
        [obj generateReport];
    }];
}
@end
```

以下是 `Task` 类的接口：

```
#import <Foundation/Foundation.h>

@interface Task : NSObject
@property(strong) NSString *name;
@property(assign) BOOL done;
-(void)generateReport;
@end
```

以下是 `Task` 类的实现：

```
#import "Task.h"

@implementation Task
-(void)generateReport{
    NSLog(@"Task %@ is %@", self.name, self.done ? @"DONE" : @"IN PROGRESS");
}
@end
```

最后，在 `main.m` 中像这样设置对象图：

```
#import <Foundation/Foundation.h>
#import "Project.h"

int main(int argc, const char * argv[]){
    @autoreleasepool {
        Project *p = [[Project alloc]init];
        p.listOfTasks = [[NSMutableArray alloc]init];
        p.name = @"Cook Dinner";
        Task *t1 = [[Task alloc]init];
        t1.name = @"Choose Menu";
        [p.listOfTasks addObject:t1];
        Task *t2 = [[Task alloc]init];
        t2.name = @"Buy Groceries";
        [p.listOfTasks addObject:t2];
        Task *t3 = [[Task alloc]init];
        t3.name = @"Prepare Ingredients";
        [p.listOfTasks addObject:t3];
        Task *t4 = [[Task alloc]init];
        t4.name = @"Cook Food";
        [p.listOfTasks addObject:t4];
        return 0;
    }
}
```

这会给你一个名为 `Cook Dinner` 的 `Project` 对象，它包含四个任务。现在你可以实施键值观察了。

### 实施键值观察

你希望当 `Task` 对象的状态发生变化时，`Project` 对象能得到通知。因此，如果一个 `Task` 对象被标记为完成，那么 `Project` 对象就会收到通知。使用键值观察需要三个步骤：

*   向每个被观察的对象发送 `addObserver:forKeyPath:options:context:` 消息。
*   在观察者对象的类定义中重写 `observeValueForKeyPath:ofObject:change:content:` 方法。
*   在观察者对象的类定义中重写 `dealloc` 方法，并移除观察者引用。

#### 添加观察者

向 `Project` 负责的每个 `Task` 发送此消息的最简单方法是使用 `listOfTasks` 的枚举方法：

```
[p.listOfTasks enumerateObjectsUsingBlock:^(id obj, NSUInteger idx, BOOL *stop) {
    [obj addObserver:p
              forKeyPath:@"done"
                 options:NSKeyValueObservingOptionNew
                 context:nil];
}];
```

这段代码可以放在 `main.m` 文件中，在所有 `Task` 对象都添加到 `Project` 对象之后。`AddObserver` 参数描述符后的第一个参数是观察者对象。下一个参数是键路径，在这里设置你想要观察的属性的键。接着是一些你可以设置的选项；你可以选择 `NSKeyValueObservingOptionNew` 来跟踪属性值的最新变化。

> **注意：** 你也可以选择 `NSKeyValueObservingOptionOld` 来获取之前的属性值，而不是像上面那样获取最新值。

#### 观察值的变化

要在属性值发生变化时收到通知，观察者对象需要在其类定义中重写一个方法。你在这里编写代码来响应变化。以下是在 `Project.m` 实现文件中执行此操作的示例：

```
#import "Project.h"

@implementation Project
-(void)observeValueForKeyPath:(NSString *)keyPath
                     ofObject:(id)object
                       change:(NSDictionary *)change
                      context:(void *)context{
    if([keyPath isEqualToString:@"done"]){
        NSNumber *updatedStatus = [change objectForKey:@"new"];
        BOOL done = [updatedStatus boolValue];
        NSLog(@"Task '%@' is now %@", [object name], done ? @"DONE" : @"IN PROGRESS");
    }
}
@end
```

> **注意：** 上述代码将添加到你本章开头添加的代码中。

每当你正在观察的 `Task` 对象改变其状态时，此方法都会执行。发生这种情况时，你首先需要测试以确保你期望的属性（`done`）确实发生了改变。你需要这样做，因为此方法可以用于各种类型的通知。

在下一行代码中，你从提供的 `NSDictionary` 变化中提取出属性的 `NSNumber` 版本，然后将其转换为你期望的 `BOOL` 类型。最后，你向控制台输出报告，报告任务的更新状态。

在测试这段代码之前，你需要进行清理工作。

#### 注销观察者

你需要确保观察者对象会遍历并停止观察每个对象，因为观察者对象很快就会被删除。执行此任务的最佳位置是在 `dealloc` 方法中。每个对象在被从程序中移除之前都会执行 `dealloc` 方法，因此这是进行此类清理工作的好时机。

以下是在 `Project.m` 实现文件中编写 `dealloc` 方法的方式：

```
-(void)dealloc{
    [self.listOfTasks enumerateObjectsUsingBlock:^(id obj, NSUInteger idx, BOOL *stop) {
        [obj removeObserver:self
                 forKeyPath:@"done"];
    }];
}
```

这将遍历 `listOfTasks` 数组，并从每个对象中移除观察者。

#### 测试观察者

要测试这一点，只需更改某些 `Task` 对象的 `done` 属性。每次这样做时，你都会看到报告写入日志。例如，假设你在 `main.m` 中这样做：

```
t4.done = YES;
t4.done = NO;
t2.done = YES;
t1.done = NO;
```

每次状态发生变化时，你的 `Project` 对象都会收到通知，这会产生以下日志输出：

```
Task 'Cook Food' is now DONE
Task 'Cook Food' is now IN PROGRESS
Task 'Buy Groceries' is now DONE
Task 'Choose Menu' is now IN PROGRESS
```

