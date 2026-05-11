# 委托

摘要：委托是一种设计模式，其中一个对象向另一个对象请求帮助。协议是委托的重要组成部分，因为协议定义了对象将如何获得帮助。

## 委托的定义

委托是一种设计模式，其中一个对象向另一个对象请求帮助。协议是委托的重要组成部分，因为协议定义了对象将如何获得帮助。

委托的工作方式是：定义一个协议，列出某个对象需要帮助的所有方法和属性。另一个对象称为委托，它通过采用并实现协议方法来提供所需的帮助。对象通过向其委托发送消息来请求帮助。

### 定义委托协议

假设你想在包含 `Project` 对象和 `Task` 对象的对象图中实现委托。在你的对象图中，`Task` 对象可能需要从 `Project` 对象获得帮助。例如，当某个 `Task` 的状态被标记为“完成”时，该任务可能不知道下一步该做什么。如果 `Project` 能够充当 `Task` 的委托，那么 `Task` 就可以向 `Project` 请求帮助。

要实现这一点，你首先需要为 `Task` 定义一个协议，明确 `Task` 需要帮助的方式。幸运的是，你已经在上一章中完成了这一步。

```
#import <Foundation/Foundation.h>
@class Task;
@protocol TaskDelegate <NSObject>
@optional
-(void)thisTask:(Task *)task statusHasChangedToThis:(BOOL)done;
@end
@interface Task : NSObject
@property(strong) NSString *name;
@property(assign) BOOL done;
-(void)generateReport;
@end
```

该协议名为 `TaskDelegate`（因为你用这个协议来定义委托如何提供帮助）。`thisTask:statusHasChangedToThis:` 是委托可以用来提供帮助的方法。

### 向委托发送消息

当 `Task` 对象需要帮助时，它们可以向委托发送消息。由于你希望在 `Task` 状态发生变化时触发此操作，可以通过为 `Task` 的 `done` 属性编写自定义属性访问器来实现。

注意：在第 16 章中，你声明了属性并让它们由自动生成的 getter 和 setter 来支持。在某些情况下，你可能希望自己编写 getter 和 setter。

你可以在 `Task` 的 `done` setter 方法中直接发送消息。

```
#import "Task.h"
@implementation Task
-(void)generateReport{
    NSLog(@"Task %@ is %@", self.name, self.done ? @"DONE" : @"IN PROGRESS");
}
BOOL _done;
-(void)setDone:(BOOL)done{
    _done = done;
    [self.delegate thisTask:self statusHasChangedToThis:done];
}
-(BOOL)done{
    return _done;
}
@end
```

在 setter 中，你可以看到正在向委托发送消息。你的 `delegate` 可以实现该方法来响应 `done` 属性更改的事件。

你可能会注意到的另一件事是，你也编写了 getter。即使不需要在 getter 中添加任何新代码，当你决定实现 setter 或 getter 中的任意一个时，必须手动实现两者。



### 分配委托对象

下一步是分配委托对象。在本例中，可以在 `main.m` 文件中完成这一操作。你只需将项目对象赋值给每个任务的 `delegate` 属性即可。

```
Project *p = [[Project alloc]init];
p.listOfTasks = [[NSMutableArray alloc]init];
p.name = @"Cook Dinner";

Task *t1 = [[Task alloc]init];
t1.name = @"Choose Menu";
t1.delegate = p;
[p.listOfTasks addObject:t1];

Task *t2 = [[Task alloc]init];
t2.name = @"Buy Groceries";
[p.listOfTasks addObject:t2];
t2.delegate = p;

Task *t3 = [[Task alloc]init];
t3.name = @"Prepare Ingredients";
[p.listOfTasks addObject:t3];
t3.delegate = p;

Task *t4 = [[Task alloc]init];
t4.name = @"Cook Food";
[p.listOfTasks addObject:t4];
t4.delegate = p;
```

现在，当你为某个任务的 `done` 属性赋予不同值时，`Project` 委托对象将收到通知。在上一章中，你已经采用了 `TaskDelegate` 协议并实现了协议方法 `-(void)thisTask:(Task *)task statusHasChangedToThis:(BOOL)done;`。

当你像这样设置 `Task` 对象的 `done` 属性时：

```
t4.done = YES;
```

你在 `Project` 实现中编写的协议方法 `thisTask:statusHasChangedToThis:` 将被执行。请记住，这是你在上一章中编写的代码：

```
-(void)thisTask:(Task *)task statusHasChangedToThis:(BOOL)done{
    NSLog(@"Task '%@' is now %@", task.name, done ? @"DONE" : @"IN PROGRESS");
}
```

该方法将在控制台日志中生成如下输出：

```
Task 'Cook Food' is now DONE
```

