# 19. 分类

## 摘要

分类用于在不使用继承的情况下扩展类。当你使用分类时，你可以在不声明父类的情况下向类添加属性和方法。

## 分类定义

分类用于在不使用继承的情况下扩展类。当你使用分类时，你可以在不声明父类的情况下向类添加属性和方法。

要定义一个分类，你需要添加一个接口和实现。你可以通过添加新的头文件和代码文件来实现，也可以直接将分类添加到你正在处理的代码文件中。



### 类别示例

举个例子，假设你想给第 16 章中定义的`Project`类添加一个构造方法，用于初始化一个新的`Project`对象并同时为其赋值名称。你可以在正在编写代码的`main.m`文件中直接添加如下代码。

首先要做的是添加接口。

```
#import <Foundation/Foundation.h>
#import "Project.h"

@interface Project(ProjectExtension)

@end

int main(int argc, const char * argv[]){
    @autoreleasepool {
        return 0;
    }
}
```

类别接口看起来与类接口类似，但遵循略有不同的格式。这些接口以`@interface`关键字开头，后跟原始类名。类名之后，括号内是类别的名称。

你在类别接口中放置前向声明。由于你需要一个能设置名称的初始化器，因此需要在类别接口中添加如下内容：

```
#import <Foundation/Foundation.h>
#import "Project.h"

@interface Project(ProjectExtension)
-(id)initWithName:(NSString *)aName;
@end

int main(int argc, const char * argv[]){
    @autoreleasepool {
        return 0;
    }
}
```

下一步是实现新方法，为此你必须编写类别实现。类别实现遵循与类别接口相同的模式。

```
#import <Foundation/Foundation.h>
#import "Project.h"

@interface Project(ProjectExtension)
-(id)initWithName:(NSString *)aName;
@end

@implementation Project (ProjectExtension)

@end

int main(int argc, const char * argv[]){
    @autoreleasepool {
        return 0;
    }
}
```

最后，你需要像实现类方法一样实现这个新方法。

```
#import <Foundation/Foundation.h>
#import "Project.h"

@interface Project(ProjectExtension)
-(id)initWithName:(NSString *)aName;
@end

@implementation Project (ProjectExtension)
-(id)initWithName:(NSString *)aName{
    self = [super init];
    if (self) {
        self.name = aName;
    }
    return self;
}
@end

int main(int argc, const char * argv[]){
    @autoreleasepool {
        return 0;
    }
}
```

现在你已经设置好类别，可以在 main 函数内部直接使用这个初始化器来创建并初始化新的`Project`类对象了。

```
#import <Foundation/Foundation.h>
#import "Project.h"

@interface Project(ProjectExtension)
-(id)initWithName:(NSString *)aName;
@end

@implementation Project (ProjectExtension)
-(id)initWithName:(NSString *)aName{
    self = [super init];
    if (self) {
        self.name = aName;
    }
    return self;
}
@end

int main(int argc, const char * argv[]){
    @autoreleasepool {
        Project *p = [[Project alloc] initWithName:@"ThisNewProject"];
        NSLog(@"p.name = %@", p.name);
        return 0;
    }
}
```

如果现在编译并运行这个项目，日志将打印出：

```
p.name = ThisNewProject
```

