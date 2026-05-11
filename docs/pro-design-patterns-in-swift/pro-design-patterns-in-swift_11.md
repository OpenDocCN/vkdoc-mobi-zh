# 8. 对象池变体

在本章中，我将解释如何对 第 7 章 中描述的对象池模式的基本实现进行变体，以管理具有不同特征的对象。每种技术都应用了一种策略来处理池实现的某个方面，以应对需要池对象的调用组件。表 8-1 将这些变体置于上下文之中。

**表 8-1.** 将对象池模式变体置于上下文

| 问题 | 答案 |
| --- | --- |
| 它们是什么？ | 对象池模式的变体允许您更改对象池的工作方式，以便在不同情况下运行。 |
| 有什么好处？ | 这些变体改变了池的行为，使其能够为具有不同期望和需求的调用组件提供服务，并管理具有一系列特征和生命周期的对象。 |
| 何时应使用这些变体？ | 当我在第 7 章中描述的模式的基本实现无法满足您的需求时，您应该使用这些变体。 |
| 何时应避免这些变体？ | 这些变体需要高级并发技术，除非您能对其进行彻底测试并对 Cocoa 并发有深入理解，否则应避免使用。 |
| 如何判断您是否已正确实现了这些变体？ | 确保您已正确实现这些变体的唯一方法是通过彻底的测试。 |
| 是否有常见的陷阱？ | 这些是高级技术，很容易误用并发保护来创建一个无法工作或性能低下的池。 |
| 是否有任何相关模式？ | 不适用。 |

## 准备示例项目

我继续使用在 第 7 章 中创建的 `ObjectPool` 项目。准备本章无需进行任何更改。

## 理解对象池模式变体

对象池的实现由四个策略组成，它们共同构成了分配对象的行为：

*   对象创建策略
*   对象重用策略
*   空池策略
*   分配策略

通过更改这些策略，您可以调整对象池模式的实现，以适应您需要管理的对象类型。我将在以下各节中解释每个策略，并演示如何实现它们。

### 理解对象创建策略

对象创建策略决定了池所管理的对象是如何创建的。在上一章中，我实现了一个积极的策略，这意味着对象在使用之前就被创建了。实际上，我在 `Library` 类中创建了 `Book` 对象，并将它们作为一个数组传递给 `Pool` 初始化器，如下所示：

```
...
private init(stockLevel:Int) {
    books = [Book]();
    for count in 1 ... stockLevel {
        books.append(Book(author: "Dickens, Charles", title: "Hard Times",
            stock: count))
    }
    pool = Pool<Book>(items:books);
}
...
```

这是我处理代表真实世界资源的对象时采用的策略，因为将要管理的对象数量通常事先已知（我的图书馆购买了两本《艰难时世》），并且需要某种程度的配置（分配唯一的库存编号）。

这种方法的缺点是，在产生任何需求之前，创建和配置对象的所有成本都已经发生。我创建了两个 `Book` 对象来代表我的查尔斯·狄更斯系列收藏，但可能很少有读者需求甚至没有，创建和准备 `Book` 对象的开销将永远不会通过请求得到回报。

这对于代表真实世界对象的对象来说通常是可接受的，因为池的状态反映了真实世界的情况：图书馆预见到读者需求而购买了两本《艰难时世》。但是，如果您使用对象池来避免承担与实际世界无关的对象的昂贵初始化成本，那么这种策略就没有帮助，例如我在 第 5 章 中用于演示原型模式的 `Sum` 对象。

另一种选择是使用**懒惰的**创建策略，这意味着对象只有在需要时才会被创建。出于我 `Library` 示例的目的，我将创建一个 `BookSeller` 类，从中可以获取 `Book` 对象。我示例中的图书馆将能够在第一次需要时从销售商那里获得所需的书籍。清单 8-1 显示了 `BookSources.swift` 文件的内容，我已将其添加到项目中。

**清单 8-1.** `BookSources.swift` 文件的内容

```
import Foundation

class BookSeller {
    class func buyBook(author:String, title:String, stockNumber:Int) -> Book {
        return Book(author: author, title: title, stock: stockNumber);
    }
}
```

`BookSeller` 类定义了一个创建 `Book` 对象的类型方法。`BookSeller` 类的实现对于示例来说并不重要——重要的是有一个 `Book` 对象的来源，它将被调用以提供池中的项目。清单 8-2 显示了我对 `Pool` 类所做的更改，以支持延迟创建项目，直到它们被需要为止。

**清单 8-2.** 在 `Pool.swift` 文件中实现懒惰对象创建

```
import Foundation

class Pool<T> {
    private var data = [T]();
    private let arrayQ = dispatch_queue_create("arrayQ", DISPATCH_QUEUE_SERIAL);
    private let semaphore:dispatch_semaphore_t;
    private var itemCount = 0;
    private let maxItemCount:Int;
    private let itemFactory: () -> T;

    init(maxItemCount:Int, factory:() -> T) {
        self.itemFactory = factory;
        self.maxItemCount = maxItemCount;
        semaphore = dispatch_semaphore_create(maxItemCount);
    }

    func getFromPool() -> T? {
        var result:T?;
        if (dispatch_semaphore_wait(semaphore, DISPATCH_TIME_FOREVER) == 0) {
            dispatch_sync(arrayQ, {() in
                if (self.data.count == 0 && self.itemCount < self.maxItemCount) {
                    result = self.itemFactory();
                    self.itemCount++;
                } else {
                    result = self.data.removeAtIndex(0);
                }
            })
        }
        return result;
    }

    func returnToPool(item:T) {
        dispatch_async(arrayQ, {() in
            self.data.append(item);
            dispatch_semaphore_signal(self.semaphore);
        });
    }

    func processPoolItems(callback:[T] -> Void) {
        dispatch_barrier_sync(arrayQ, {() in
            callback(self.data);
        });
    }
}
```


我已根据您的要求，对提供的文本进行了排版处理。以下是处理后的 Markdown 格式内容。

---

我已修改了 `Pool` 的初始化器，使其接收一个闭包，该闭包可用于为对象池创建新元素，并接收一个 `Int` 参数，用于指定闭包可被调用的最大次数。我将 Grand Central Dispatch 信号量的初始计数值设为最大元素数量，并在 `getFromPool` 方法中对对象池的状态进行了更复杂的检查，具体如下：

```
if (self.data.count == 0 && self.itemCount < self.maxItemCount) {
    result = self.itemFactory();
    self.itemCount++;
} else {
    result = self.data.removeAtIndex(0);
}
```

要执行到方法中的这一步，线程必须已通过信号量，这意味着要么 `data` 数组中存在一个等待使用的对象，要么我需要调用工厂闭包创建一个新对象。这使我能够将对象池中元素的创建延迟到有实际需求时再进行。

对 `Pool` 类的另一处修改是实现了一个名为 `processPoolItems` 的类型方法。在原始实现中，对象池中对象的创建职责归属于 `Library` 类，该类会保留所有由它负责的 `Book` 对象的引用，并能够利用这些本地引用生成报告。而在当前实现中，创建对象的职责归属于 `Pool` 类，`Library` 类根本不持有任何对象引用——因此我添加了 `processPoolItems` 方法，该方法接收一个回调闭包，并在同步 GCD 屏障代码块中将 `data` 数组传递给它。

> **提示**  
> 你可以在代码清单 8-3 中看到我是如何实现 `Book` 工厂闭包的。我本可以在对象创建时保留对它们的引用，但 Swift 处理初始化器中定义的闭包的方式使得这一过程较为困难。因此，我让 `Pool` 类成为示例应用中 `Book` 对象详细信息的权威来源。

代码清单 8-3 展示了 `Library` 类相应的改动，以完成懒加载创建策略的实现。

**代码清单 8-3. 在 Library.swift 文件中实现懒加载对象创建**

```
import Foundation

final class Library {
    private let pool:Pool<Book>;
    private init(stockLevel:Int) {
        var stockId = 1;
        pool = Pool<Book>(maxItemCount: stockLevel, factory: {() in
            return BookSeller.buyBook("Dickens, Charles",
                title: "Hard Times", stockNumber: stockId++)
        });
    }

    private class var singleton:Library {
        struct SingletonWrapper {
            static let singleton = Library(stockLevel:200);
        }
        return SingletonWrapper.singleton;
    }

    class func checkoutBook(reader:String) -> Book? {
        var book = singleton.pool.getFromPool();
        book?.reader = reader;
        book?.checkoutCount++;
        return book;
    }

    class func returnBook(book:Book) {
        book.reader = nil;
        singleton.pool.returnToPool(book);
    }

    class func printReport() {
        singleton.pool.processPoolItems({(books) in
            for book in books {
                println("...Book#\(book.stockNumber)...");
                println("Checked out \(book.checkoutCount) times");
                if (book.reader != nil) {
                    println("Checked out to \(book.reader!)");
                } else {
                    println("In stock");
                }
            }
            println("There are \(books.count) books in the pool");
        });
    }
}
```

改动幅度较小。我定义了将通过 `BookSeller` 类用于创建 `Book` 对象的闭包，并更新了 `printReport` 方法，使其从对象池中获取数据项。此外，我将对象池中书籍的最大数量增加到了 200 本，这远高于 `main.swift` 文件中的代码所产生的需求。

> **注意**  
> 这模拟了现实世界中图书馆与书商约定信用额度，并按需调用的场景。图书馆最初没有任何库存，但每当有借书请求且库存中没有书籍时，书商就会应要求送来一本，直到达到预设上限——在本例中是 200 本。然而，只有在库存中没有书时才会要求送来新书——如果已有副本可用，图书馆会重新借出现有副本。

通过运行应用程序，你可以看到懒加载策略的效果。在所有请求处理完毕后（可能需要 15 到 20 秒），生成的报告结尾会声明对象池中有多少 `Book` 对象。

```
...
There are 14 books in the pool
```

每次运行应用程序时，具体的书籍数量都会有所不同，因为我会随机给部分请求添加延迟，这会影响对象池对 `Book` 对象的复用频率。

最坏情况下会创建 20 本书，这意味着图书馆愿意购买的 200 本书是一个严重的高估。如果采用积极策略，全部 200 本副本都会被购买；而采用懒加载策略，我能够更贴近实际需求。


### 理解对象复用策略

对象池模式的本质意味着由池管理的对象会被重复分配给使用者，这带来了对象以不良状态返回的风险。以现实世界的图书馆为例，这可能意味着书页被撕毁或缺失。在软件中，这意味着对象的状态不一致，或遇到了无法恢复的错误。

最简单的方法是信任策略，即你信任对象会以可复用的状态返回池中。这并不总是一个坏主意，因为并非所有对象都需要处理这类问题。目前由池管理的 `Book` 对象就是很好的例子，因为它们几乎不暴露可变公共状态。

另一种方法是怀疑策略，即在对象返回池中之前对其进行检查，以确保它们能被再次使用。无法复用的对象将从池中被剔除。

**注意**

怀疑策略应仅用于有能力替换被剔除对象的池。如果没有替换对象的能力，池可能会耗尽条目，应用程序也可能陷入停滞。详情请参见“理解空池策略”一节。

我将改变 `Book` 对象的使用方式，使它们只能从池中被借出特定次数，这反映了书籍会磨损并最终达到无法阅读状态的事实。我不想在通用的 `Pool` 类中构建任何关于 `Book` 类的知识，因此在一个名为 `PoolItem.swift` 的新文件中定义了一个名为 `PoolItem` 的协议，其内容如代码清单 8-4 所示。

**代码清单 8-4. `PoolItem.swift` 文件的内容**

```
@objc protocol PoolItem {
    var canReuse:Bool {get}
}
```

这个 `PoolItem` 协议定义了一个名为 `canReuse` 的只读属性。当条目被返回到池中时，我将读取此属性，并丢弃任何返回 `false` 的对象。代码清单 8-5 展示了 `Pool` 类中的相应更改。

**提示**

请注意，我已将 `@objc` 属性应用于此协议。这将允许我将对象向下转型为协议类型，以便在池类中读取 `canReuse` 属性。

**代码清单 8-5. 在 `Pool.swift` 文件中添加对 `PoolItem` 协议的支持**

```
import Foundation

class Pool<T:AnyObject> {
    private var data = [T]();
    private let arrayQ = dispatch_queue_create("arrayQ", DISPATCH_QUEUE_SERIAL);
    private let semaphore:dispatch_semaphore_t;
    private var itemCount = 0;
    private let maxItemCount:Int;
    private let itemFactory: () -> T;

    init(maxItemCount:Int, factory:() -> T) {
        self.itemFactory = factory;
        self.maxItemCount = maxItemCount;
        semaphore = dispatch_semaphore_create(maxItemCount);
    }

    func getFromPool() -> T? {
        var result:T?;
        if (dispatch_semaphore_wait(semaphore, DISPATCH_TIME_FOREVER) == 0) {
            dispatch_sync(arrayQ, {() in
                if (self.data.count == 0 && self.itemCount < self.maxItemCount) {
                    result = self.itemFactory();
                    self.itemCount++;
                } else {
                    result = self.data.removeAtIndex(0);
                }
            })
        }
        return result;
    }

    func returnToPool(item:T) {
        dispatch_async(arrayQ, {() in
            let pitem = item as AnyObject as? PoolItem;
            if (pitem == nil || pitem!.canReuse) {
                self.data.append(item);
                dispatch_semaphore_signal(self.semaphore);
            }
        });
    }

    func processPoolItems(callback:[T] -> Void) {
        dispatch_barrier_sync(arrayQ, {() in
            callback(self.data);
        });
    }
}
```

我不想限制池可以处理的对象范围，因此仅当实现了 `PoolItem` 协议时，我才会检查可复用性。检查协议一致性只能在对协议应用 `@objc` 属性时进行，而应用该属性意味着它只能由类而非结构体实现。为了给 Swift 提供检查一致性所需的类型信息，我限制了 `Pool` 的泛型参数。

```
...
class Pool<T:AnyObject> {
...
```

`AnyObject` 协议意味着 `Pool` 只能处理基于类的对象，这并非重大限制，因为对值类型进行池化毫无意义，毕竟在赋值给变量时它们会被复制。要检查对 `PoolItem` 协议的一致性，我需要给 Swift 一点帮助。

```
...
let pitem = item as AnyObject as? PoolItem;
if (pitem == nil || pitem!.canReuse) {
    self.data.append(item);
    dispatch_semaphore_signal(self.semaphore);
}
...
```

如果我将 `as?` 运算符直接用于 item 对象（其类型为 `T`——即 `Pool` 类中的泛型类型），则编译器会生成错误。我需要先将其转换为 `AnyObject`（这可以保证成功，因为类型 `T` 被限制为实现 `AnyObject` 协议的类），然后使用 `as?` 运算符来查看 `PoolItem` 协议是否已实现。

其效果是，如果未实现 `PoolItem` 协议，或者实现了且 `canReuse` 属性的值为 `true`，则条目会返回池中。

#### 应用协议

下一步是应用 `PoolItem` 协议，以便 `Book` 对象在被从池中剔除之前可以被借出固定次数。代码清单 8-6 展示了我如何修改 `Book` 类以实现该协议。

**提示**

请注意，我已将 `@objc` 属性添加到 `Book` 类。这是为了在 `Pool` 类中支持对 `PoolItem` 协议的一致性。

**代码清单 8-6. 在 `Book.swift` 文件中实现协议**

```
import Foundation;

@objc class Book : PoolItem {
    let author:String;
    let title:String;
    let stockNumber:Int;
    var reader:String?
    var checkoutCount = 0;

    init(author:String, title:String, stock:Int) {
        self.author = author;
        self.title = title;
        self.stockNumber = stock;
    }

    var canReuse:Bool {
        get {
            let reusable = checkoutCount < 5
            if (!reusable) {
                println("Eject: Book#\(self.stockNumber)");
            }
            return reusable;
        }
    }
}
```

如果某个 `Book` 对象被从池中借出超过五次，我会从 `canReuse` 属性返回 `false`，并且为了能看到条目何时被剔除，我会在控制台写一条消息。

#### 测试该策略

为了测试从池中剔除条目的策略，我需要平衡池中将包含的最大条目数与 `main.swift` 文件中代码将发出的请求数量。我需要有足够的书籍来允许其中一些被剔除，同时在系统中留出足够的余量来处理所有请求。在“理解空池策略”一节中，我将通过形式化一种策略来展示如何正确处理这个问题，但当前我会将池中的最大条目数设置为 `5`（这是通过反复试验确定的）。代码清单 8-7 展示了我在 `Library` 类中更改限制的方法。

**代码清单 8-7. 在 `Library.swift` 文件中更改池中的最大条目数**

```
...
private class var singleton:Library {
    struct SingletonWrapper {
        static let singleton = Library(stockLevel:5);
    }
    return SingletonWrapper.singleton;
}
...
```

运行该应用程序将产生类似于以下内容的输出：

```
Starting...
Eject: Book#1
Eject: Book#2
All blocks complete
...Book#3...
Checked out 3 times
In stock
...Book#4...
Checked out 4 times
In stock
...Book#5...
Checked out 3 times
In stock
There are 3 books in the pool
Program ended with exit code: 0
```

由于条目返回池前的随机延迟，你可能会得到不同的结果，但基本结果应该相同：部分 `Book` 对象被借出的次数多于其他对象，当它们被借出五次时，会被从池中剔除，并由池创建的新对象替换。



### 理解空池策略

顾名思义，空池策略规定了当池中没有可用对象来服务于新对象请求时的应对方式。最简单的策略就是我的示例池所使用的阻塞策略，该策略强制调用线程等待，直到有对象被归还到池中。

阻塞策略虽然简单，但如果池中对象的数量与对这些对象的需求水平不匹配，可能会导致应用程序性能下降。如果阻塞策略与我在上一节中描述的不信任语句管理策略结合使用，还可能导致应用程序锁死。

在前面的示例中，我通过试错法确定了池可以创建的 `Books` 对象的最大数量。这只需片刻时间，因为我能够多次运行应用程序，从而了解来自 `main.swift` 文件的对象请求如何影响对象从池中被弹出的速率。在实际应用中，你只能猜测需求，并且必须为突发的高需求时期留出余量，这会给池对象带来额外压力。为了查看可能出现的这种问题，我增加了从池中请求对象的次数，如代码清单 8-8 所示。

**代码清单 8-8.** 在 `main.swift` 文件中增加请求次数

```
...
for i in 1 ... 35 {
    dispatch_group_async(group, queue, {() in
        var book = Library.checkoutBook("reader#\(i)");
        if (book != nil) {
            NSThread.sleepForTimeInterval(Double(rand() % 2));
            Library.returnBook(book!);
        }
    });
}
...
```

我将请求次数增加到了 35 次，因为这会超过池中对象可被使用的次数。池最多允许创建五个对象，每个对象最多可使用五次。这意味着从第 26 次请求开始，后续所有请求都将无法获得可用对象。运行应用程序即可看到效果，输出如下：

```
Starting...
Eject: Book#4
Eject: Book#5
Eject: Book#1
Eject: Book#3
Eject: Book#2
```

请注意，这里没有每本书的状态摘要。这是因为所有五本书都已被弹出了池，而尝试获取对象的 GCD 块无法通过 `getFromPool` 方法中的信号量。应用程序已陷入死锁：在池发出信号量指示有空闲对象之前，GCD 块不会被执行，但池已达到其可创建对象数量的上限。

#### 实现请求失败策略

请求失败策略通过将责任转移给请求对象的组件来处理空池情况。该组件必须指定在请求失败之前，它愿意等待对象变为可用的最长时间。请求失败意味着组件必须为无法获取所需对象的情况做好准备，并制定继续操作或报告错误的计划。代码清单 8-9 展示了如何修改 `Pool` 类中 `getFromPool` 方法的实现来实施此策略。

**代码清单 8-9.** 在 `Pool.swift` 文件中实现该策略

```
...
func getFromPool(maxWaitSeconds:Int = 5) -> T? {
    var result:T?;
    let waitTime = (maxWaitSeconds == -1)
        ? DISPATCH_TIME_FOREVER
        : dispatch_time(DISPATCH_TIME_NOW,
            (Int64(maxWaitSeconds) * Int64(NSEC_PER_SEC)));
    if (dispatch_semaphore_wait(semaphore, waitTime) == 0) {
        dispatch_sync(arrayQ, {() in
            if (self.data.count == 0 && self.itemCount < self.maxItemCount) {
                result = self.itemFactory();
                self.itemCount++;
            } else {
                result = self.data.removeAtIndex(0);
            }
        })
    }
    return result;
}
...
```

> **警告**
> 此策略应谨慎使用，因为从池中消费对象的组件必须编写相应代码来处理失败的请求。我有时会看到组件将对对象的请求包裹在循环中，以便不断请求直到获取到对象。这等同于发出一个永远等待的请求，应用程序仍然会锁死——只是换了一种不同且更耗费 CPU 的方式而已。

我为 `getFromPool` 方法添加了一个名为 `maxWaitSeconds` 的参数，并设置了默认值，允许调用者指定他们愿意等待对象变为可用的秒数。我将值 `-1` 解释为永远等待，并将任何其他值解释为从当前时间起算的秒数。

GCD 定义了 `DISPATCH_TIME_FOREVER` 常量来表示无限期等待，而对于任何其他时长，我使用 `dispatch_time` 函数来创建相应值，该函数的参数是一个初始时间和要增加纳秒数：

```
...
let waitTime = (maxWaitSeconds == -1)
    ? DISPATCH_TIME_FOREVER
    : dispatch_time(DISPATCH_TIME_NOW,
        (Int64(maxWaitSeconds) * Int64(NSEC_PER_SEC)));
...
```

我使用 `DISPATCH_TIME_NOW` 常量作为第一个参数，以指定我希望时间相对于当前时刻，然后将调用者指定的秒数乘以 `NSEC_PER_SEC` 常量（该常量定义了一秒中有多少纳秒）来获得纳秒数。

> **警告**
> `DISPATCH_TIME_NOW` 常量只能用作 `dispatch_time` 函数的参数。如果在函数外部使用它，它将返回零值。

我将 `waitTime` 值作为第二个参数传递给 `dispatch_semaphore_wait` 函数。

```
...
if (dispatch_semaphore_wait(semaphore, waitTime) == 0) {
...
```

`dispatch_semaphore_wait` 函数将阻塞，直到 `returnToPool` 方法中发出信号量信号，或者达到指定时间。如果使用 `DISPATCH_TIME_FOREVER` 值，那么信号量将永久阻塞，这是我在前面章节中所依赖的行为。对于其他 `waitTime` 值，`dispatch_semaphore_wait` 函数将在指定时间段过后允许线程继续执行。



我需要知道信号量为何允许线程继续执行。如果是因为池中有可用对象，那么我想获取该对象并将其返回给调用者；如果是因为时间已到，那么我希望方法不向可选的`result`变量赋值就返回。通过检查`dispatch_semaphore_wait`函数返回的结果，我可以判断发生了什么情况。返回值为`0`表示信号量已被触发，非零值表示时间已到。

`getFromPool`方法的签名已经返回一个可选的`Book`对象，这意味着`Library`类已经准备好处理对`checkoutBook`方法的调用，即使该方法无法从池中获取`Book`对象。

```
class func checkoutBook(reader:String) -> Book? {
    var book = singleton.pool.getFromPool();
    book?.reader = reader;
    book?.checkoutCount++;
    return book;
}
```

`checkoutBook`方法的返回结果也是一个可选的`Book`对象，这使得在`main.swift`文件中检测超时请求变得容易，如清单 8-10 所示。

**清单 8-10. 在 main.swift 文件中处理过期请求**

```
import Foundation

var queue = dispatch_queue_create("workQ", DISPATCH_QUEUE_CONCURRENT);
var group = dispatch_group_create();
println("Starting...");
for i in 1 ... 35 {
    dispatch_group_async(group, queue, {() in
        var book = Library.checkoutBook("reader#\(i)");
        if (book != nil) {
            NSThread.sleepForTimeInterval(Double(rand() % 2));
            Library.returnBook(book!);
        } else {
            dispatch_barrier_async(queue, {() in
                println("Request \(i) failed");
            });
        }
    });
}
dispatch_group_wait(group, DISPATCH_TIME_FOREVER);
dispatch_barrier_sync(queue, {() in
    println("All blocks complete");
    Library.printReport();
});
```

如果我没有从`Library`类接收到`Book`对象，那么我会向控制台写入一条消息，指示请求失败。`println`函数用于将消息写入控制台，但它不是并发安全的，因此我将该函数调用封装在一个 GCD 块中。我使用了 barrier，因为队列是并发的，我不希望两个失败的请求导致同时调用`println`函数，这会产生混乱的输出。

> **提示**
> 
> 请注意，我也将对`Library.printReport`方法的调用封装在了 GCD 块中。该方法使用`println`函数将报告写入调试控制台，因此我使用了相同的 GCD 队列来确保生成报告时不会与正在写入的失败请求消息同时调用`println`函数。

如果你运行该应用程序，将会看到类似如下的输出：

```
Starting...
Eject: Book#4
Eject: Book#1
Eject: Book#5
Eject: Book#2
Eject: Book#3
All blocks complete
Request 26 failed
Request 30 failed
Request 28 failed
Request 31 failed
Request 29 failed
Request 27 failed
Request 32 failed
Request 33 failed
Request 34 failed
Request 35 failed
There are 0 books in the pool
```

这次应用程序运行完成，无法获取对象的请求会失败。应用程序不再死锁，但这意味着`Library`类的使用者必须被编写成能够处理`Book`对象可能不可用的情况，并知道如何应对。

#### 处理池资源耗尽

我在上一节中实施的策略存在一个问题：即使池已耗尽，它也会要求调用者等待。此处的“耗尽”是指所有对象都已被弹出，并且由于已达到初始化时设置的限制，池无法再创建更多对象。这远非理想情况，因为即使池永久性耗尽，那些希望等到对象可用的调用仍然被允许继续等待。清单 8-11 展示了如何增强`Pool`类，使其在池耗尽时拒绝请求。

**清单 8-11. 在 Pool.swift 文件中处理池枯竭**

```
import Foundation

class Pool<T:AnyObject> {
    private var data = [T]();
    private let arrayQ = dispatch_queue_create("arrayQ", DISPATCH_QUEUE_SERIAL);
    private let semaphore:dispatch_semaphore_t;
    private var itemCount = 0;
    private let maxItemCount:Int;
    private let itemFactory: () -> T;
    private var ejectedItems = 0;
    private var poolExhausted = false;

    init(maxItemCount:Int, factory:() -> T) {
        self.itemFactory = factory;
        self.maxItemCount = maxItemCount;
        semaphore = dispatch_semaphore_create(maxItemCount);
    }

    func getFromPool(maxWaitSeconds:Int = -1) -> T? {
        var result:T?;
        let waitTime = (maxWaitSeconds == -1)
            ? DISPATCH_TIME_FOREVER
            : dispatch_time(DISPATCH_TIME_NOW,
                (Int64(maxWaitSeconds) * Int64(NSEC_PER_SEC)));
        if (!poolExhausted) {
            if (dispatch_semaphore_wait(semaphore, waitTime) == 0) {
                if (!poolExhausted) {
                    dispatch_sync(arrayQ, {() in
                        if (self.data.count == 0
                            && self.itemCount < self.maxItemCount) {
                            result = self.itemFactory();
                            self.itemCount++;
                        } else {
                            result = self.data.removeAtIndex(0);
                        }
                    })
                }
            }
        }
        return result;
    }

    func returnToPool(item:T) {
        dispatch_async(arrayQ, {() in
            if let pitem = item as AnyObject as? PoolItem {
                if (pitem.canReuse) {
                    self.data.append(item);
                    dispatch_semaphore_signal(self.semaphore);
                } else {
                    self.ejectedItems++;
                    if (self.ejectedItems == self.maxItemCount) {
                        self.poolExhausted = true;
                        self.flushQueue();
                    }
                }
            } else {
                self.data.append(item);
            }
        });
    }

    private func flushQueue() {
        var dQueue = dispatch_queue_create("drainer", DISPATCH_QUEUE_CONCURRENT);
        var backlogCleared = false;
        dispatch_async(dQueue, {() in
            dispatch_semaphore_wait(self.semaphore, DISPATCH_TIME_FOREVER);
            backlogCleared = true;
        });
        dispatch_async(dQueue, {() in
            while (!backlogCleared) {
                dispatch_semaphore_signal(self.semaphore);
            }
        });
    }

    func processPoolItems(callback:[T] -> Void) {
        dispatch_barrier_sync(arrayQ, {() in
            callback(self.data);
        });
    }
}
```

清单中的更改解决了两个问题：识别池何时耗尽以及拒绝所有待处理的请求。为了识别池何时耗尽，我在`returnToPool`方法中跟踪从池中被拒绝的对象数量，当池能够创建的所有对象都已被弹出时，我将一个名为`poolExhausted`的实例变量设置为`true`，并调用一个名为`flushQueue`的新方法。

> **提示**
> 
> 我必须重写`returnToPool`方法以处理三种可能的情况。第一种是对象实现了`PoolItem`协议并且可以重用。第二种是对象实现了`PoolItem`协议但无法重用——并且可能是最后一个被弹出的对象。最后一种情况是该对象未实现`PoolItem`协议，这种情况下弹出和耗尽无关紧要，该对象可以直接加回池中。

我修改了`getFromPool`方法，使得请求在等待信号量之前会检查`poolExhausted`属性的值，这意味着池耗尽之后到达的任何新请求都会立即返回。



第二个问题涉及处理对象池耗尽前到达的未完成请求。发起请求的线程将等待 GCD 信号量，并在收到信号前持续等待。不幸的是，GCD 信号量没有提供唤醒所有等待线程的方法，因此我在实现`flushQueue`方法时不得不采用间接方式。

我创建了一个独立的 GCD 队列，并向其中添加了两个块。第一个块等待信号量，当信号量允许其通过时，将名为`backlogCleared`的局部变量设置为`true`。

```
dispatch_async(dQueue, {() in
    dispatch_semaphore_wait(self.semaphore, DISPATCH_TIME_FOREVER);
    backlogCleared = true;
});
```

GCD 信号量允许线程按先进先出顺序通过，这意味着在对象池耗尽前到达的所有等待请求都被允许通过之前，这个块将无法通过信号量。

第二个块重复发送信号量，直到`backlogCleared`属性的值发生变化。

```
dispatch_async(dQueue, {() in
    while (!backlogCleared) {
        dispatch_semaphore_signal(self.semaphore);
    }
});
```

我添加这些块的队列是并发的，信号量将持续发送直到积压清除。我希望防止正在等待信号量的调用向数组修改队列添加块，这就是为什么在`getFromPool`方法中等待信号量前后检查`poolExhausted`属性的值。

```
if (!poolExhausted) {
    if (dispatch_semaphore_wait(semaphore, waitTime) == 0) {
        if (!poolExhausted) {
            ...
```

为了更轻松地测试新功能，我将`getFromPool`方法中`maxWaitSeconds`参数的默认值改为`-1`；仅在对象池耗尽时请求才会被拒绝。运行应用程序，你会看到与之前示例类似的输出——但这种实现方式防止了愿意等待对象的调用者在对象池耗尽时被锁定。

#### 创建弹性对象池

如果对象池能够根据需要创建所需数量的对象，则不必拒绝请求。这被称为弹性对象池，它适用于有首选数量和单独最大数量的场景。

在软件开发方面，弹性对象池可用于任何通过创建额外对象来应对需求增长的场景。一个常见例子是网络连接，在正常操作时有首选连接数，在高峰期有一定余量。

从现实图书馆的角度看，热门书籍的高峰需求可以通过从附近分馆借书来满足。这并非理想方案，因为它会降低图书馆网络其他地方的可用性，但意味着在特殊情况下无需长时间等待就能满足借书请求。为了将这个场景映射到我的示例中，我在`BookSources.swift`文件中添加了一个名为`LibraryNetwork`的类，如代码清单 8-12 所示。

**代码清单 8-12. BookSources.swift 文件内容**

```swift
import Foundation

class BookSeller {
    class func buyBook(author:String, title:String, stockNumber:Int) -> Book {
        return Book(author: author, title: title, stock: stockNumber);
    }
}

class LibraryNetwork {
    class func borrowBook(author:String, title:String, stockNumber:Int) -> Book {
        return Book(author: author, title: title, stock: stockNumber);
    }
    class func returnBook(book:Book) {
        // do nothing
    }
}
```

`LibraryNetwork`类是一个占位符，用于演示临时获取额外书籍。我不会在这个类中实现任何逻辑，因为我的重点是对象池。代码清单 8-13 展示了如何在`Pool`类中实现弹性。

**代码清单 8-13. 在 Pool.swift 文件中添加对象弹性**

```swift
import Foundation

class Pool<T:AnyObject> {
    private var data = [T]();
    private let arrayQ = dispatch_queue_create("arrayQ", DISPATCH_QUEUE_SERIAL);
    private let semaphore:dispatch_semaphore_t;
    private let itemFactory: () -> T;
    private let peakFactory: () -> T;
    private let peakReaper:(T) -> Void;
    private var createdCount:Int = 0;
    private let normalCount:Int;
    private let peakCount:Int;
    private let returnCount:Int;
    private let waitTime:Int;
    
    init(itemCount:Int, peakCount:Int, returnCount: Int, waitTime:Int = 2,
        itemFactory:() -> T, peakFactory:() -> T, reaper:(T) -> Void) {
        self.normalCount = itemCount; self.peakCount = peakCount;
        self.waitTime   = waitTime; self.returnCount = returnCount;
        self.itemFactory = itemFactory; self.peakFactory = peakFactory;
        self.peakReaper  = reaper;
        self.semaphore   = dispatch_semaphore_create(itemCount);
    }
    
    func getFromPool() -> T? {
        var result:T?;
        let expiryTime = dispatch_time(DISPATCH_TIME_NOW,
            (Int64(waitTime) * Int64(NSEC_PER_SEC)));
        if (dispatch_semaphore_wait(semaphore, expiryTime) == 0) {
            dispatch_sync(arrayQ, {() in
                if (self.data.count == 0) {
                    result = self.itemFactory();
                    self.createdCount++;
                } else {
                    result = self.data.removeAtIndex(0);
                }
            })
        } else {
            dispatch_sync(arrayQ, {() in
                result = self.peakFactory();
                self.createdCount++;
            });
        }
        return result;
    }
    
    func returnToPool(item:T) {
        dispatch_async(arrayQ, {() in
            if (self.data.count > self.returnCount
                && self.createdCount > self.normalCount) {
                self.peakReaper(item);
                self.createdCount--;
            } else {
                self.data.append(item);
                dispatch_semaphore_signal(self.semaphore);
            }
        });
    }
    
    func processPoolItems(callback:[T] -> Void) {
        dispatch_barrier_sync(arrayQ, {() in
            callback(self.data);
        });
    }
}
```

> **注意**：我已移除处理对象池耗尽和剔除对象的代码，以简化示例，并且这些功能通常不与弹性一起使用（因为代码会变得复杂且难以维护）。



该对象池使用 GCD 信号量等待一段指定时间内有对象可用，默认等待时间为三秒。如果在有对象可用之前，`semaphore` 的 `wait` 操作超时，则会使用一个工厂闭包创建临时对象来满足需求。（这里有两个工厂闭包——一个用于常规的惰性对象创建，另一个用于创建临时峰值对象）。

弹性对象池的核心特征在于对临时对象的处理方式。你可以将它们永久保留在池中，也可以释放引用使其被删除，或者——就像我在此处所做的——定义一个回收器，用于在不再需要这些对象时进行处置。在本例中，我接受将回收器作为初始化参数传入，并在对对象的需求下降时调用它。

> **提示**
>
> 你可以制定复杂的策略来决定何时不再需要临时对象，但我建议尽可能保持简单。复杂的方案在开发期间效果良好，但面对难以提前预测的实际使用模式时，往往容易崩溃。

要使用弹性对象池，我需要修改 `Library` 类中的初始化器，以提供额外的闭包和对象数量，如代码清单 8-14 所示。

**代码清单 8-14.** 在 `Library.swift` 文件中更新 Library 初始化器

```
...
private init(stockLevel:Int) {
    var stockId = 1;
    pool = Pool<Book>(
        itemCount:stockLevel,
        peakCount: stockLevel * 2,
        returnCount: stockLevel / 2,
        itemFactory: {() in
            return BookSeller.buyBook("Dickens, Charles",
                title: "Hard Times", stockNumber: stockId++)},
        peakFactory: {() in
            return LibraryNetwork.borrowBook("Dickens, Charles",
                title: "Hard Times", stockNumber: stockId++)},
        reaper: LibraryNetwork.returnBook
    );
}
...
```

常规对象池中的对象是从 `BookSeller` 类获取的 `Book` 对象。当需求达到正常水平的两倍时，会通过 `LibraryNetwork.borrowBook` 方法获取额外的 `Book` 对象；当对象数量降至正常水平的 50% 时，这些额外对象将由 `LibraryNetwork.returnBook` 方法回收。

运行应用程序可以看到对象弹性特性的效果，其输出如下：

```
...Starting...
All blocks complete
...Book#2...
Checked out 4 times
In stock
...Book#15...
Checked out 1 times
In stock
...Book#19...
Checked out 1 times
In stock
...Book#17...
Checked out 1 times
In stock
...Book#18...
Checked out 1 times
In stock
There are 5 books in the pool
...
```

此示例借用了 `Book` 对象来满足需求，这体现在输出中显示的书籍被从对象池中借出的次数。`main.swift` 文件中的代码请求了 35 个 `Book` 对象，但报告仅显示 8 次借出。其余请求是通过从 `LibraryNetwork` 类获取对象来满足的，后续这些对象又被归还给了该类。

## 理解分配策略

分配策略决定了如何选择一个可用对象来服务请求。本章到目前为止使用的策略是先进先出，其原理是将数组当作队列来处理。这种方法的优点是实现简单，但缺点是可能导致对象分配不均，使得某些对象被借出的次数远多于其他对象。

对于大多数应用来说，FIFO 分配策略是合适的，但某些应用需要不同的对象分配方法。以我的示例应用为例，我打算分配使用次数最少的可用书籍，但——正如你将看到的——当对对象的需求很高时，这种策略的影响是有限的。代码清单 8-15 展示了如何在 `Pool` 类中添加对自定义分配策略的支持。

**代码清单 8-15.** 在 `Pool.swift` 文件中添加对分配策略的支持

```
import Foundation

class Pool<T:AnyObject> {
    private var data = [T]();
    private let arrayQ = dispatch_queue_create("arrayQ", DISPATCH_QUEUE_SERIAL);
    private let semaphore:dispatch_semaphore_t;
    private let itemFactory: () -> T;
    private let itemAllocator:[T] -> Int;
    private let maxItemCount:Int;
    private var createdCount:Int = 0;
    
    init(itemCount:Int, itemFactory:() -> T, itemAllocator:([T] -> Int)) {
        self.maxItemCount = itemCount;
        self.itemFactory = itemFactory;
        self.itemAllocator = itemAllocator;
        self.semaphore = dispatch_semaphore_create(itemCount);
    }
    
    func getFromPool() -> T? {
        var result:T?;
        if (dispatch_semaphore_wait(semaphore, DISPATCH_TIME_FOREVER) == 0) {
            dispatch_sync(arrayQ, {() in
                if (self.data.count == 0) {
                    result = self.itemFactory();
                    self.createdCount++;
                } else {
                    result = self.data.removeAtIndex(self.itemAllocator(self.data));
                }
            })
        }
        return result;
    }
    
    func returnToPool(item:T) {
        dispatch_async(arrayQ, {() in
            self.data.append(item);
            dispatch_semaphore_signal(self.semaphore);
        });
    }
    
    func processPoolItems(callback:[T] -> Void) {
        dispatch_barrier_sync(arrayQ, {() in
            callback(self.data);
        });
    }
}
```

这是一个固定大小的对象池，采用惰性创建对象的方式。我定义了一个名为 `itemAllocator` 的初始化参数，它是一个闭包，会接收可用对象的数组。该闭包返回数组中应被分配用于服务请求的对象所在位置。代码清单 8-16 展示了如何更新 `Library` 类的初始化器来实现分配策略。

**代码清单 8-16.** 在 `Library.swift` 文件中添加分配策略

```
...
private init(stockLevel:Int) {
    var stockId = 1;
    pool = Pool<Book>(
        itemCount:stockLevel,
        itemFactory: {() in
            return BookSeller.buyBook("Dickens, Charles",
                title: "Hard Times", stockNumber: stockId++)},
        itemAllocator: {(var books) in return 0; }
    );
}
...
```

我从 FIFO 策略开始，该闭包选择数组中的第一个对象。我建议你从该策略入手，以便衡量任何替代方案的影响。根据向对象池发出的请求模式，你很可能会发现 FIFO 分配策略能带来接近你目标的效果。以下是使用 FIFO 运行应用程序时输出的格式化版本：

```
...
...Book#4...Checked out 10 times
...Book#3...Checked out 6 times
...Book#2...Checked out 5 times
...Book#1...Checked out 5 times
...Book#5...Checked out 9 times
...
```

各个对象的使用次数存在一些差异，但幅度不大。在实际项目中，根据这些结果，我会考虑坚持使用 FIFO 策略（不过我也会观察生产环境中的差异，以确保我的测试符合实际情况）。



### 清单 8-17 展示了一种最少使用策略。该策略会选择可用的对象中使用次数最少的那个。可能存在一些已被借出但使用次数更少的对象。将所有对象都考虑在内的策略被称为完美分配策略，但这种策略几乎没什么用，因为它意味着你需要阻塞一个请求，直到绝对最少使用的对象变得可用，而这可能在相当长一段时间内都不会发生。

## 清单 8-17 在`Library.swift`文件中实现不同的分配策略

```
...
private init(stockLevel:Int) {
    var stockId = 1;
    pool = Pool<Book>(
        itemCount:stockLevel,
        itemFactory: {() in
            return BookSeller.buyBook("Dickens, Charles",
                title: "Hard Times", stockNumber: stockId++)},
        itemAllocator: {(var books) in
            var selected = 0;
            for index in 1 ..< books.count {
                if (books[index].checkoutCount < books[selected].checkoutCount) {
                    selected = index;
                }
            }
            return selected;
        }
    );
}
...
```

在新的闭包中，我检查了每个可用的对象，并选择了`checkoutCount`属性值最低的那个。以下是使用此策略后的输出结果：

```
...
...Book#2...Checked out 8 times
...Book#3...Checked out 5 times
...Book#5...Checked out 8 times
...Book#4...Checked out 8 times
...Book#1...Checked out 6 times
...
```

由于`main.swift`代码中的随机性，具体结果会有所不同，但总体效果是更均匀地分配对象。这是一种更均衡的分配策略，但每次分配对象时需要做更多工作，并且（至少对于我的测试代码而言）影响相对较小。这并不意味着你应该始终使用 FIFO 分配策略，但你应该确保额外的工作是值得的。

## 理解模式变种的陷阱

这些变种带来的主要风险是复杂性，这可能会表现为并发问题，以及代码更难阅读和维护。除了这些问题之外，这些策略还可能导致一些特定的陷阱，如下文所述。

### 理解预期差距陷阱

正如本章所展示的，可以创建在表面上看相似，但在幕后实现不同策略的对象池。你必须确保这些策略的外部影响对调用组件是显而易见的，否则你会发现对象池的有效性会受到损害。

例如，你可能会选择通过拒绝对象请求来处理空池。如果你这样做，请确保池对象的`checkout`方法的返回类型是可选类型，例如`Book?`，而不仅仅是`Book`。使用可选结果可以清楚地表明，对该方法的调用可能无法获得对象。

并非所有行为都能用语言特性来表达，我建议你提供 API 文档，详细描述池的工作方式。例如，你需要记录这样一个事实：当池耗尽时，你通过拒绝请求来处理，以避免调用组件使用`for`循环直到获得对象——这种行为可能导致应用程序死锁。

### 理解过度使用与使用不足陷阱

一旦你开始实现对象池模式，就很容易在情感上投入过多精力，去选择和创建你需要的策略，并让它们完美配合。一个编写良好的对象池模式实现堪称艺术品，但你必须时刻关注池在测试和部署期间的表现，因为很容易创建一个实现完美但会损害应用程序性能或消耗过多资源的池。

过度使用的池是一个性能隐患，因为它大部分时间都处于空池状态，并且有一长串调用组件在等待对象。使用不足的池是一个资源隐患，因为它管理着一组很少被使用的对象。平衡对象池的大小和行为与其工作负载需要一些反复试验——以及用于测试的真实使用数据。此外，你必须准备好根据应用程序的需求改变池的大小和行为，即使这意味着要放弃一个你特别引以为傲的策略。

## Cocoa 中模式变种的示例

在 Cocoa 框架中，没有明显的这些模式变种的使用实例，不过很难确定，因为苹果没有发布源代码。

## 将模式变种应用于 SportsStore

作为本章的结尾，我将更改第 7 章中添加到 SportsStore 应用程序的池的对象创建策略。目前，`NetworkPool`类会急切地创建其对象。

```
...
private init() {
    for _ in 0 ..< connectionCount {
        connections.append(NetworkConnection());
    }
    semaphore = dispatch_semaphore_create(connectionCount);
    queue = dispatch_queue_create("networkpoolQ", DISPATCH_QUEUE_SERIAL);
}
...
```

我将切换为延迟创建策略。在本章前面部分，我对通用池类使用了闭包来实现这一点，因为我不想让池直接了解其管理的对象是如何创建的。相比之下，SportsStore 的`NetworkPool`类已经知道`NetworkConnection`类及其创建方式，这简化了策略的实现。清单 8-18 展示了为实现新策略所做的更改。

## 清单 8-18 在`NetworkPool.swift`文件中更改对象创建策略

```
import Foundation

final class NetworkPool {
    private let connectionCount = 3;
    private var connections = [NetworkConnection]();
    private var semaphore:dispatch_semaphore_t;
    private var queue:dispatch_queue_t;
    private var itemsCreated = 0;

    private init() {
        semaphore = dispatch_semaphore_create(connectionCount);
        queue = dispatch_queue_create("networkpoolQ", DISPATCH_QUEUE_SERIAL);
    }

    private func doGetConnection() -> NetworkConnection {
        dispatch_semaphore_wait(semaphore, DISPATCH_TIME_FOREVER);
        var result:NetworkConnection? = nil;
        dispatch_sync(queue, {() in
            if (self.connections.count > 0) {
                result = self.connections.removeAtIndex(0);
            } else if (self.itemsCreated < self.connectionCount) {
                result = NetworkConnection();
                self.itemsCreated++;
            }
        });
        return result!;
    }

    private func doReturnConnection(conn:NetworkConnection) {
        dispatch_async(queue, {() in
            self.connections.append(conn);
            dispatch_semaphore_signal(self.semaphore);
        });
    }

    class func getConnection() -> NetworkConnection {
        return sharedInstance.doGetConnection();
    }

    class func returnConnecton(conn:NetworkConnection) {
        sharedInstance.doReturnConnection(conn);
    }

    private class var sharedInstance:NetworkPool {
        get {
            struct SingletonWrapper {
                static let singleton = NetworkPool();
            }
            return SingletonWrapper.singleton;
        }
    }
}
```

这些改动很简单。我移除了初始化器中急切创建`NetworkConnection`对象的代码，并添加了在`doGetConnection`方法中延迟创建它们的代码。操作一个管理特定类型的池类总是更容易，但我仍然更喜欢使用通用池类，因为这意味着我总能获得一致的池实现（因为我从一个项目到另一个项目复制代码），对我来说，这值得增加额外的复杂性。

## 本章小结

在本章中，我向你展示了如何更改定义对象池工作方式的四个策略，以便你可以使用模式的不同实现来处理各种对象。很少有模式像对象池模式那样需要如此多的细节，我将在下一章回到更简单的领域，描述工厂方法模式。



