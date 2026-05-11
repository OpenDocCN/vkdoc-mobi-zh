# 继承

**摘要**

当你想编写一个共享另一个类大部分属性和方法的新类时，可以使用继承。从另一个类继承的类继承了父类的所有属性和方法。

当你想要利用已经完成的工作，并通过添加更多属性和方法来自定义新类时，可以使用继承。这种模式实现了代码复用。

你在第 16 章中看到了继承的例子：当你创建 `Project` 类时，你继承了 `NSObject`。这使得 `Project` 拥有了 `NSObject` 的所有方法和属性。

当你将此技术应用于对象图时，会产生更有趣的应用。例如，假设现在你想创建一个与 `Project` 类似但有一些关键区别的新类。

## 创建子类

要创建一个新的子类，你可以遵循第 16 章中列出的相同模式。你需要定义一个接口和实现。这里的区别在于，你将选择 `Project` 而不是 `NSObject` 作为父类。

```
#import "Project.h"

@interface SpecialProject : Project

@end
```

在上述接口中需要注意两点：你导入的是 `Project.h`（而不是之前的 `Foundation`），并且你现在在冒号后面的是 `Project` 而不是 `NSObject`，这表明 `Project` 是父类，而 `SpecialProject` 是你的子类。

`SpecialProject` 的实现很简单，与你为原始 `Project` 类所做的类似。

```
#import "SpecialProject.h"

@implementation SpecialProject

@end
```

如果你现在使用 `SpecialProject`，它的行为会与 `Project` 完全一样。现在，你可以添加额外的属性和方法来自定义 `SpecialProject`。这被称为扩展类。



## 扩展类

要扩展一个类，你可以向子类中添加属性和方法。你的子类是 `SpecialProject`。现在，我们为你的 `SpecialProject` 类添加一个名为 `generateSpecialReport` 的方法。

```
#import "Project.h"

@interface SpecialProject : Project

-(void)generateSpecialReport;

@end
```

当然，接下来你需要实现这个方法。

```
#import "SpecialProject.h"

@implementation SpecialProject

-(void)generateSpecialReport{
    NSLog(@"This is a special report!");
}

@end
```

扩展类的过程与你通常添加属性和方法的方式完全相同。这个方法之所以强大，在于你可以共享某一类对象中通用的方法代码。

### 重写方法

你还可以做另一件事：重写父类中的方法。重写方法意味着你将编写一个与父类方法*签名完全相同*（即参数描述符的集合）的自有版本。由于你编写的方法代码不同，这意味着即使子类对象收到与父类相同的消息，它们的行为也会有所不同。

假设你想确保 `SpecialProject` 对象总能打印出特殊报告，即使发送的是来自父类 `Project` 的 `generateReport` 消息。

首先，你需要在 `SpecialProject` 的接口中编写 `generateReport` 方法。

```
#import "Project.h"

@interface SpecialProject : Project

-(void)generateSpecialReport;
-(void)generateReport;

@end
```

然后，你需要为 `generateReport` 编写一个新的实现，并让该方法向 `generateSpecialReport` 发送一条消息。在这种情况下，你通常还会向父类（`super`）的该方法实现发送一条消息，这可以通过向 `super` 发送消息来实现。

```
#import "SpecialProject.h"

@implementation SpecialProject

-(void)generateSpecialReport{
    NSLog(@"This is a special report!");
}

-(void)generateReport{
    [super generateReport];
    [self generateSpecialReport];
}

@end
```

#### 实例变量的可见性

在第 16 章中讨论实例变量（或 ivars）时，其使用场景非常直接。如果你的类需要数据存储，而这些数据并非描述对象的属性（因此不应该成为属性），那么你只需使用一个实例变量即可。

由于你将实例变量添加到了类扩展中，因此只有在该文件中实现的类才能使用它们。这些实例变量被认为是*私有的*，因为它们仅对编写它们的类可见。

##### 可见性级别

当你在对象图中计划使用继承时，你可能希望实例变量具有不同的可见性级别。“可见性”指的是其他实体对该变量的访问权限。实例变量可以是私有的、受保护的或公有的。当你希望拥有不同的可见性级别时，你必须在接口中编写实例变量，该接口应位于头文件中，因为其他类导入的正是这个文件。

###### 私有实例变量

私有实例变量只能在其被编写的类中使用。要使实例变量成为私有的，你需要在类接口中声明该实例变量。假设你想添加一些 `NSString` 对象，作为所有 `Project` 类的日志。要为 `Project` 对象添加一个私有日志，你需要在 `Project` 接口中这样操作：

```
#import <Foundation/Foundation.h>

@interface Project : NSObject {
    @private NSString *log1;
}

@property(strong) NSString *name;
-(void)generateReport;
-(void)generateReportAndAddThisString:(NSString *)string
                   andThenAddThisDate:(NSDate *)date;
+(void)printTimeStamp;

@end
```

你必须使用 `@private` 关键字来指定 `log1` 将具有私有可见性。这意味着你可以在 `Project` 的方法中使用 `log1`，但不能在 `SpecialProject` 的方法中使用。

###### 受保护的实例方法

具有受保护可见性的实例变量可以被其所属类以及任何派生类中的方法访问。因此，如果你想让一个 `NSString` 日志变量既能被 `Project` 使用，也能被 `SpecialProject` 使用，你需要使用 `@protected` 关键字使其受保护。

```
#import <Foundation/Foundation.h>

@interface Project : NSObject{
    @private NSString *log1;
    @protected NSString *log2;
}

@property(strong) NSString *name;
-(void)generateReport;
-(void)generateReportAndAddThisString:(NSString *)string
                   andThenAddThisDate:(NSDate *)date;
+(void)printTimeStamp;

@end
```

###### 公有实例变量

公有实例变量对其所属类以及所有派生类都是可用的。此外，其他对象可以直接引用公有实例变量。

> **注意：** 我在此讨论公有实例变量是为了内容的完整性，但通常不推荐以这种方式使用实例变量。相反，你应该定义属性来返回任何你想让其他对象访问的值。

要使一个实例变量成为公有的，你必须使用 `@public` 关键字。

```
#import <Foundation/Foundation.h>

@interface Project : NSObject{
    @private NSString *log1;
    @protected NSString *log2;
    @public NSString *log3;
}

@property(strong) NSString *name;
-(void)generateReport;
-(void)generateReportAndAddThisString:(NSString *)string
                   andThenAddThisDate:(NSDate *)date;
+(void)printTimeStamp;

@end
```

要使用这个对象，你需要使用成员操作符 `->`。成员操作符是一种传统的 C 语言操作，可用于引用由指针所指向的结构体成员。以下是使用示例：

```
#import <Foundation/Foundation.h>
#import "SpecialProject.h"

int main(int argc, const char * argv[]){
    @autoreleasepool {
        SpecialProject *sp = [[SpecialProject alloc]init];
        NSString *tempLog = sp->log3;
        NSLog(@"temp = %@", tempLog);
        return 0;
    }
}
```

