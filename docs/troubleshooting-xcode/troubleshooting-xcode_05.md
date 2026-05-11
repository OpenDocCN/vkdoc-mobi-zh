# Objective-C 中不可能的“常量”

与 Swift 不同，你无法使用 Objective-C 将一个像 `NSArray`、`NSDictionary` 这样的集合声明为常量；但我们认为我们或许有一个解决方案。

该方案涉及创建一个基于 `NSObject` 的类，并声明静态变量来备份和模拟一个“常量”集合。

## Constants.h

```
#import <Foundation/Foundation.h>

@interface Constants : NSObject

+ (NSArray *) myConstantArray;

@end
```

## Constants.m

```
#import "Constants.h"

@implementation Constants

+ (NSArray *) myConstantArray {
     // 通过将静态变量声明在实现文件中，我们保证
     // 使用此类的其他类不会创建该静态变量的副本
  static NSArray *_myConstantArray = nil;
  @synchronized (_myConstantArray) {
    if (_myConstantArray == nil) {
      _myConstantArray = @[ /* 在这里声明你的数组 */ ];
    }
    return _myConstantArray;
  }
}

@end
```

要使用这个“常量”，你必须使用以下形式：

```
NSArray *anArray = [MyConstantClass myConstantArray];
```

