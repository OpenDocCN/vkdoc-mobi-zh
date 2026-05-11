# 对象归档

摘要

保存应用程序对象图的副本以供日后作为备份使用，称为对象归档。Objective-C 拥有可以帮助你归档对象图的类。每个支持归档的类都必须采用`NSCoding`协议，并实现`NSKeyedArchiver`和`NSKeyedUnarchiver`类所需的两个方法。

## 对象归档定义

保存应用程序对象图的副本以供日后作为备份使用，称为对象归档。Objective-C 拥有可以帮助你归档对象图的类。每个支持归档的类都必须采用`NSCoding`协议，并实现`NSKeyedArchiver`和`NSKeyedUnarchiver`类所需的两个方法。



### NSCoding

每个支持归档的类都必须遵循`NSCoding`协议并实现必需的方法。这些方法将帮助归档程序存储需要归档的对象中保存的属性值。

要遵循`NSCoding`协议，请在类接口的尖括号中添加`NSCoding`协议名称。以下是你已经编码的`Task`类的实现方式：

```
#import <Foundation/Foundation.h>

@interface Task : NSObject <NSCoding>

@property(strong) NSString *name;
@property(assign) BOOL done;
-(void)generateReport;

@end
```

所遵循的`NSCoding`协议的语法如上所示（以粗体显示）。现在你需要实现两个协议方法。第一个方法称为编码器（encoder），因为它将用于将属性值编码到归档文件中。

```
#import "Task.h"

@implementation Task

-(void)generateReport{
    NSLog(@"Task %@ is %@", self.name, self.done ? @"DONE" : @"IN PROGRESS");
}

-(void)encodeWithCoder:(NSCoder *)encoder {
    [encoder encodeObject:self.name forKey:@"namekey"];
    [encoder encodeBool:self.done forKey:@"donekey"];
}

@end
```

你想要保存到归档中的每个属性都需要发送一个编码消息并附带一个键（key）。不同的数据类型需要不同的消息。有关可用方法的完整列表，请参阅 Apple 的`NSCoding`文档。例如，对象需要`encodeObject:forKey:`消息，而布尔值需要`encodeBool:forKey:`消息。

接下来，你需要实现解码器方法。该方法是一种构造器初始化方法，它会将属性值添加到对象中。与之前一样，重要方法以粗体显示。

```
#import "Task.h"

@implementation Task

-(void)generateReport{
    NSLog(@"Task %@ is %@", self.name, self.done ? @"DONE" : @"IN PROGRESS");
}

-(void)encodeWithCoder:(NSCoder *)encoder {
    [encoder encodeObject:self.name forKey:@"namekey"];
    [encoder encodeBool:self.done forKey:@"donekey"];
}

-(id)initWithCoder:(NSCoder *)decoder {
    self = [super init];
    if (self) {
        self.name = [decoder decodeObjectForKey:@"namekey"];
        self.done = [decoder decodeBoolForKey:@"donekey"];
    }
    return self;
}

@end
```

此方法中的每个键和属性必须与`encodeWithCoder:`方法中的键和属性匹配。至此，`Task`已经支持`NSCoding`。为了查看示例，你还需要为`Project`添加`NSCoding`支持。以下是`Project`接口的实现方式：

```
#import <Foundation/Foundation.h>
#import "Task.h"

@interface Project : NSObject <NSCoding>

@property(strong) NSString *name;
@property(strong) NSMutableArray *listOfTasks;
-(void)generateReport;

@end
```

以下是`Project`实现的实现方式：

```
#import "Project.h"

@implementation Project

-(void)generateReport{
    NSLog(@"Report for %@ Project", self.name);
    [self.listOfTasks enumerateObjectsUsingBlock:^(id obj, NSUInteger idx, BOOL *stop) {
        [obj generateReport];
    }];
}

-(void)encodeWithCoder:(NSCoder *)encoder {
    [encoder encodeObject:self.name forKey:@"namekey"];
    [encoder encodeObject:self.listOfTasks forKey:@"listOfTaskskey"];
}

-(id)initWithCoder:(NSCoder *)decoder {
    self = [super init];
    if (self) {
        self.name = [decoder decodeObjectForKey:@"namekey"];
        self.listOfTasks = [decoder decodeObjectForKey:@"listOfTaskskey"];
    }
    return self;
}

@end
```

### 使用归档器（Archiver）

假设你已经建立了一个对象图，那么使用归档器就很简单了。你使用的类是`NSKeyedArchiver`，只需发送`archiveRootObject:toFile:`消息即可。两个参数分别是你要使用的文件名和对象图中的根对象。你的根对象是`Project p`对象，因为你只有一个`p`，它包含多个`Task`对象。

我们假设你仍然拥有第 24 章中创建的对象图，该章列出了`p`的所有任务。如果你想归档这些内容，可以在`main.m`文件中执行以下操作：

```
NSString *file = @"/Users/Shared/project.dat";
[NSKeyedArchiver archiveRootObject:p toFile:file];
```

这将在你的 Mac 上创建一个数据文件。将来，你可以读取这个文件并用它来将对象图恢复到你的应用中。

```
NSString *file = @"/Users/Shared/project.dat";
Project *p = [NSKeyedUnarchiver unarchiveObjectWithFile:file];
[p generateReport];
```

由于你在此处发送了`generateReport`消息，你将在控制台日志中看到对象图的打印输出。

```
Report for Cook Dinner Project
Task Choose Menu is IN PROGRESS
Task Buy Groceries is IN PROGRESS
Task Prepare Ingredients is IN PROGRESS
Task Cook Food is IN PROGRESS
```

