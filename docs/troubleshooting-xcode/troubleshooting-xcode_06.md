# Core Data 的并发处理

有时，你的应用程序可能在执行 Core Data 操作时崩溃，且原因不明。

我们现在要探讨的一种可能性是，应用程序可能同时从不同的线程访问了 `NSManagedObjectContext` 对象。

为了从多线程访问 Core Data 实体，Apple 建议你使用线程隔离。

Core Data 推荐的并发编程模式是线程隔离：每个线程都必须拥有自己完全私有的托管对象上下文。

采用这种模式有两种可能的方法：

- 为每个线程创建一个独立的托管对象上下文，并共享一个持久化存储协调器。这是通常推荐的方法。
- 为每个线程创建一个独立的托管对象上下文和持久化存储协调器。这种方法提供了更高的并发性，但代价是更高的复杂性（特别是在不同上下文之间通信变更时）和更高的内存使用率。

在你花时间开发支持线程隔离的必要代码之前，你可能需要确认你的应用遇到的问题是否真的是由多个线程同时访问 Core Data 上下文对象引起的。

## 代码实现

基本上，这段代码是一个用于控制上下文访问的锁机制。同一时间只有一个线程能够访问上下文。

你必须找到代码中访问上下文的位置，并添加以下用**粗体**标出的行：

#### Swift

**Entity.swift**

```
import Foundation
import CoreData

@objc(Entity)
class Entity: NSManagedObject {

    static let lock = NSLock()

    func allObjectsInContext(context : NSManagedObjectContext)
               -> NSArray {

        var all : NSArray?
        // 准备一个获取请求
        let request : NSFetchRequest? = NSFetchRequest()
        request?.entity =
                NSEntityDescription.entityForName("Entity",
                inManagedObjectContext: context)
        request?.returnsDistinctResults = true

        Entity.lock.lock()

        do {
            // 这就是你访问上下文的地方
            all = try context.executeFetchRequest(request!)
            // 成功...
        } catch let error as NSError { // 失败
            print("获取失败: \(error.localizedDescription)")
        }

        Entity.lock.unlock()

        return all!
    }
}
```

#### Objective-C

**Entity.h**

```
#import <Foundation/Foundation.h>
#import <CoreData/CoreData.h>

NS_ASSUME_NONNULL_BEGIN

@interface Entity : NSManagedObject

+ (NSArray *)allObjectsInContext:(NSManagedObjectContext *)
                                       context;

@end

NS_ASSUME_NONNULL_END
```

**Entity.m**

```
#import "Entity.h"

@implementation Entity

+ (NSArray *)allObjectsInContext:(NSManagedObjectContext *)
                                       context
{
  NSArray *all = nil;

  // 准备一个获取请求
  NSFetchRequest *request = [[NSFetchRequest alloc] init];
  request.entity = [NSEntityDescription
                   entityForName:NSStringFromClass([self class])
          inManagedObjectContext:context];
  [request setReturnsDistinctResults:YES];

  NSError *error = nil;

  // 这条指令将锁住上下文，防止
  // 对其的多次访问
  @synchronized(context) {
      // 这就是你访问上下文的地方
      all = [context executeFetchRequest:request error:&error];
  }

  return all;
}

@end
```

> **注意：** 请注意，这个机制可能会降低你的应用程序速度，尤其是在处理数据库密集型应用时。请仅将其用于调试目的。
>
> 如果在添加 `@synchronized` 这行代码后你的代码不再崩溃，那么问题就得到了确认，我们建议你遵循 Apple 的建议，为每个线程创建独立的上下文。

