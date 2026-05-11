# 第 18 章 键值编码

我们反复提及的一个概念是间接性。许多编程技术都基于间接性，整个面向对象编程也不例外。在本章中，我们将探讨另一种间接机制。这不是 Objective-C 的语言特性，而是 Cocoa 提供的一种机制。

到目前为止，我们一直在通过直接调用方法、使用属性的点语法或设置实例变量来直接改变对象的状态。而键值编码（朋友们亲切地称之为`KVC`）是一种通过使用字符串来描述要改变的对象状态片段，从而间接改变对象状态的方法。本章的全部内容就是关于键值编码的。

一些更高级的 Cocoa 特性，如 Core Data 和 Cocoa Bindings（本书中不会讨论），在其基础机制中使用了`KVC`作为关键组件。

## 一个入门项目

我们将再次与老朋友`CarParts`合作。请查看名为`18.01 Car-Value-Coding`的项目以获取相关资源。为了启动项目，我们为`Car`类添加了一些属性，比如品牌和型号，以便进行实验。我们将`appellation`重命名为`name`，以使代码更加统一：

```objc
@interface Car : NSObject <NSCopying>
{
    NSString *name;
    NSMutableArray *tires;
    Engine *engine;
    NSString *make;
    NSString *model;
    int modelYear;
    int numberOfDoors;
    float mileage;
}

@property (readwrite, copy) NSString *name;
@property (readwrite, retain) Engine *engine;
@property (readwrite, copy) NSString *make;
@property (readwrite, copy) NSString *model;
@property (readwrite) int modelYear;
@property (readwrite) int numberOfDoors;
@property (readwrite) float mileage;

…

@end // Car
```

我们还添加了`@synthesize`指令，以便编译器自动生成 setter 和 getter 方法：

```objc
@implementation Car

@synthesize name;
@synthesize engine;
@synthesize make;
@synthesize model;
@synthesize modelYear;
@synthesize numberOfDoors;
@synthesize mileage;
```

我们还更新了`-copyWithZone`方法，以复制新的属性：

```objc
- (id) copyWithZone: (NSZone *) zone
{
    Car *carCopy;
    carCopy = [[[self class] allocWithZone: zone] init];
    carCopy.name = name;
    carCopy.make = make;
    carCopy.model = model;
    carCopy.numberOfDoors = numberOfDoors;
    carCopy.mileage = mileage;
    
    // 加上复制轮胎和引擎的代码，详见第 13 章。
```

我们还修改了`-description`方法，使其打印这些新属性，并且省略了`Engine`和`Tire`的打印：

```objc
- (NSString *) description
{
    NSString *desc;
    desc = [NSString stringWithFormat:
        @"%@, a %d %@ %@, has %d doors, %.1f miles, and %d tires.",
        name, modelYear, make, model, numberOfDoors, mileage, [tires count]];
    return desc;
} // description
```

最后，在`main`函数中，我们将为汽车设置这些属性并打印出来。我们还使用了`autorelease`配合`alloc`和`init`，以便将所有内存管理集中在一处：

```objc
int main (int argc, const char * argv[])
{
    @autoreleasepool
    {
        Car *car = [[[Car alloc] init] autorelease];
        car.name = @"Herbie";
        car.make = @"Honda";
        car.model = @"CRX";
        car.numberOfDoors = 2;
        car.modelYear = 1984;
        car.mileage = 110000;
        
        int i;
        for (i = 0; i < 4; i++)
        {
            AllWeatherRadial *tire;
            tire = [[AllWeatherRadial alloc] init];
            [car setTire: tire atIndex: i];
            [tire release];
        }
        
        Slant6 *engine = [[[Slant6 alloc] init] autorelease];
        car.engine = engine;
        
        NSLog (@"Car is %@", car);
    }
    return (0);
} // main
```

运行程序后，你会得到类似下面的一行输出：

```
Car is Herbie, a 1984 Honda CRX, has 2 doors, 110000.0 miles, and 4 tires.
```

## 引入 KVC

键值编码中的基本调用是`-valueForKey:`和`-setValue:forKey:`。你向对象发送消息，并传入一个字符串作为感兴趣的属性键。

因此，我们可以这样获取汽车的名称：

```objc
NSString *name = [car valueForKey:@"name"];
NSLog (@"%@", name);
```

这会输出`Herbie`。同样地，我们可以获取品牌：

```objc
NSLog (@"make is %@", [car valueForKey:@"make"]);
```



`valueForKey:` 施展了一点魔法，它会找出 `make` 属性的值并将其返回。

`valueForKey:` 的工作方式是首先查找与键同名的 getter 方法：`-key` 或 `-isKey`。因此，对于这两个调用，`valueForKey:` 会查找 `-name` 和 `-make` 方法。如果没有 getter 方法，它会在对象内部查找名为 `_key` 或 `key` 的实例变量。如果我们没有通过 `@synthesize` 提供访问器方法，`valueForKey:` 会查找实例变量 `_name` 和 `name`，或 `_make` 和 `make`。

最后一点非常重要：`-valueForKey:` 利用 Objective-C 运行时中的元数据来剖析对象，并在其内部寻找有用的信息。在 C 或 C++ 中，你很难做到这一点。通过使用 KVC，即使没有 getter 方法，你也可以获取值，而无需通过对象指针直接访问实例变量。

同样的技术也适用于车型年份：

```
NSLog (@"model year is %@", [car valueForKey: @"modelYear"]);
```

这将打印出 `model year is 1984`。

嘿，等一下！`NSLog` 中的 `%@` 用于打印对象，但 `modelYear` 是一个 `int`，而不是对象。这是怎么回事？对于 KVC，Cocoa 会自动对标量值进行装箱和拆箱。也就是说，当你使用 `valueForKey:` 时，它会自动将标量值（`int`、`float` 和一些 `struct`）放入 `NSNumber` 或 `NSValue` 中；当你使用 `-setValueForKey:` 时，它会自动从这些对象中取出标量值。只有 KVC 才能进行这种自动装箱。常规的方法调用和属性语法则不会。

除了检索值，你还可以通过 `-setValue:forKey:` 按名称设置值。

```
[car setValue: @"Harold" forKey: @"name"];
```

这个方法的工作方式与 `-valueForKey:` 相同。它首先查找 `name` 的 setter 方法，比如 `-setName`，并用参数 `@"Harold"` 调用它。如果没有 setter 方法，它会在类中查找名为 `name` 或 `_name` 的实例变量并为其赋值。

**我们必须强调这条规则**：编译器和苹果都保留以下划线开头的实例变量名，并警告说，如果你试图使用它们，你和你（的狗）将面临严重后果。这条规则目前并未强制执行，但未来可能某天就会生效，因此请自行承担违规风险。

如果你要设置一个标量值，在调用 `-setValue:forKey:` 之前，需要先将其打包（装箱）：

```
[car setValue: [NSNumber numberWithFloat: 25062.4] forKey: @"mileage"];
```

然后 `-setValue:forKey:` 会在调用 `-setMileage:` 或修改 `mileage` 实例变量之前对该值进行拆箱。

### 路径！路径！

除了通过键设置值，键值编码还允许你指定键路径，它就像文件系统路径一样，让你能够沿着关系链进行追踪。

为了有点东西可挖，不如给我们的引擎加点马力？我们将向 `Engine` 添加一个新的实例变量：

```
@interface Engine : NSObject <NSCopying>
{
    int horsepower;
}
@end // Engine
```

注意，我们没有添加任何访问器或属性。通常，你会希望为感兴趣的对象属性添加访问器或属性，但我们这里故意省略它们，是为了向你展示 KVC 可以直接深入对象内部。

我们添加了一个 `init` 方法，以便引擎在初始化时马力不为零：

```
- (id) init
{
    if (self = [super init])
    {
        horsepower = 145;
    }
    return (self);
} // init
```

我们还在 `-copyWithZone:` 中添加了对 `horsepower` 实例变量的复制，以便复制体也能获取该值，并且将其添加到了 `-description` 方法中。这些现在都是老生常谈了，所以我们不再赘述。

为了证明我们可以获取和设置该值，你可以将以下代码添加到 `18.01 Car-Value-Coding.m` 中：

```
NSLog (@"horsepower is %@", [engine valueForKey: @"horsepower"]);
[engine setValue: [NSNumber numberWithInt: 150] forKey: @"horsepower"];
NSLog (@"horsepower is %@", [engine valueForKey: @"horsepower"]);
```

这将打印出：

```
horsepower is 145
horsepower is 150
```

那么那些键路径呢？你可以用点分隔来指定不同的属性名。通过向汽车询问其 `"engine.horsepower"`，你就能得到马力值。事实上，让我们尝试使用 `-valueForKeyPath:` 和 `-setValueForKeyPath:` 方法来访问键路径。我们将向汽车而不是引擎发送这些消息：

```
[car setValue: [NSNumber numberWithInt: 155] forKeyPath: @"engine.horsepower"];
NSLog (@"horsepower is %@", [car valueForKeyPath: @"engine.horsepower"]);
```

这些键路径的深度可以是任意的，具体取决于你对象图的复杂性（这其实就是对你相关对象集合的一种花哨说法）；你可以拥有像 `"car.interior.airconditioner.fan.velocity"` 这样的键路径。在某些方面，使用键路径深入对象内部比进行一系列嵌套的方法调用要简单得多。

### 聚合攻击

KVC 的一个很酷的地方是，如果你向 `NSArray` 请求某个键的值，它实际上会询问数组中每个对象该键的值，然后将结果打包到另一个数组中返回给你。这对于通过键路径访问对象内部（还记得组合吗？）的数组也同样适用。

嵌入在其他对象中的 `NSArray`，在 KVC 术语中被称为具有**对多**关系。例如，一辆汽车与多个（嗯，四个）轮胎存在关系。因此我们可以说，`Car` 与 `Tire` 之间存在对多关系。如果键路径包含一个数组属性，则键路径的剩余部分会被发送给数组中的每个对象。

**众多之中，唯一**既然你现在了解了对多关系，你可能想知道什么是对一关系。普通的对象组合就是对一关系。例如，一辆汽车与其引擎之间存在对一关系。

回想一下，`Car` 有一个轮胎数组，每个轮胎都有自己的气压。我们可以通过一次调用来获取所有轮胎的气压：

```
NSArray *pressures = [car valueForKeyPath: @"tires.pressure"];
```

在做出以下调用后：

```
NSLog (@"pressures %@", pressures);
```

我们可以打印出这些结果：

```
pressures (
    34,
    34,
    34,
    34
)
```

除了我们很注意轮胎保养之外，这里到底发生了什么？`valueForKeyPath:` 会分解你的路径并从左到右处理。首先，它向汽车询问其轮胎。一旦获得了轮胎，它就会使用键路径的剩余部分 `"pressure"`（这里指这个字符串）向 `tires` 对象发送 `valueForKeyPath:` 消息。`NSArray` 通过遍历其内容并向每个对象发送消息来实现 `valueForKeyPath:`。因此，`NSArray` 会向其内部的每个轮胎发送一个键路径为 `"pressure"` 的 `valueForKeyPath:` 消息，结果返回用 `NSNumber` 装箱的轮胎气压。非常方便！

不幸的是，你无法在键路径中对这些数组进行索引，比如使用 `"tires[0].pressure"` 来获取第一个轮胎。

### 中途补给

在进入下一节键值的好东西之前，我们将添加一个新类，名为 `Garage`，它将存放一批经典汽车。你可以在此项目中找到所有相关内容：`16.02 Car-Value-Garaging`。以下是 `Garage` 的接口：

```
#import <Cocoa/Cocoa.h>

@class Car;

@interface Garage : NSObject
{
    NSString *name;
    NSMutableArray *cars;
}

@property (readwrite, copy) NSString *name;
- (void) addCar: (Car *) car;
- (void) print;

@end // Garage
```

这里没有什么新内容。我们前向声明了 `Car`，因为我们只需要知道它是一个对象类型，可以用作 `-addCar:` 方法的参数。`name` 是一个属性，`@property` 语句表示 `Garage` 的用户可以访问和修改 `name`。还有一个用于打印内容的方法。为了实现一个汽车集合，我们在后台使用了一个可变数组。

实现也同样简单明了：

```
#import "Garage.h"

@implementation Garage

@synthesize name;

- (void) addCar: (Car *) car
{
    if (cars == nil)
    {
        cars = [[NSMutableArray alloc] init];
    }
    [cars addObject: car];
} // addCar

- (void) dealloc
{
    [name release];
    [cars release];
    [super dealloc];
} // dealloc

- (void) print
{
    NSLog (@"%@:", name);
```



```objectivec
for (Car *car in cars)
{
    NSLog (@" %@", car);
}
} // print
@end // Car
```

我们像往常一样包含了`Garage.h`头文件，并使用`@synthesize`来合成名称访问器方法。

`-addCar:`是`cars`数组惰性初始化的一个示例；我们只在必要时才创建它。`-dealloc`清理名称和数组，而`-print`遍历数组并打印出所有汽车。

与之前版本的程序相比，我们还彻底改造了主源文件`Car-Value-Garaging.m`。这次，程序创建了一个汽车集合，并将它们放入车库中。

首先，针对我们要使用的对象，引入必要的`#import`指令：

```objectivec
#import <Foundation/Foundation.h>
#import "Car.h"
#import "Garage.h"
#import "Slant6.h"
#import "Tire.h"
```

接下来，我们有一个函数，用于根据一系列属性创建一辆汽车。我们本可以在`Car`上创建一个类方法，或者创建某种工厂类，但 Objective-C 仍然是 C 语言，因此我们可以使用函数。在这里，我们使用函数，因为它将组装汽车的代码保留在实际使用它的位置附近。

```objectivec
Car *makeCar (NSString *name, NSString *make, NSString *model, int modelYear, int numberOfDoors, float mileage, int horsepower)
{
    Car *car = [[[Car alloc] init] autorelease];

    car.name = name;
    car.make = make;
    car.model = model;
    car.modelYear = modelYear;
    car.numberOfDoors = numberOfDoors;
    car.mileage = mileage;

    Slant6 *engine = [[[Slant6 alloc] init] autorelease];
    [engine setValue: [NSNumber numberWithInt: horsepower]
              forKey: @"horsepower"];

    car.engine = engine;

    // 制作一些轮胎。
    for (int i = 0; i < 4; i++)
    {
        Tire * tire= [[[Tire alloc] init] autorelease];
        [car setTire: tire atIndex: i];
    }

    return (car);

} // makeCar
```

到目前为止，大部分内容应该都很熟悉了。按照 Cocoa 的惯例，创建一辆新车并自动释放，因为从此函数获取汽车的调用者不会自己调用`new`、`copy`或`alloc`。然后，我们设置一些属性——注意，此技术与 KVC 不同，因为我们没有使用`setValue:forKey`。接下来，我们制作一个引擎，并使用 KVC 来设置马力，因为我们没有为它创建访问器。最后，我们制作一些轮胎并将其安装到汽车上。最后，返回这辆新车。

以下是`main()`函数的新版本：

```objectivec
int main (int argc, const char * argv[])
{
    @autoreleasepool
    {
        Garage *garage = [[Garage alloc] init];

        garage.name = @"Joe's Garage";

        Car *car;

        car = makeCar (@"Herbie", @"Honda", @"CRX", 1984, 2, 110000, 58);
        [garage addCar: car];

        car = makeCar (@"Badger", @"Acura", @"Integra", 1987, 5, 217036.7, 130);
        [garage addCar: car];

        car = makeCar (@"Elvis", @"Acura", @"Legend", 1989, 4, 28123.4, 151);
        [garage addCar: car];

        car = makeCar (@"Phoenix", @"Pontiac", @"Firebird", 1969, 2, 85128.3, 345);
        [garage addCar: car];

        car = makeCar (@"Streaker", @"Pontiac", @"Silver Streak", 1950, 2, 39100.0, 36);
        [garage addCar: car];

        car = makeCar (@"Judge", @"Pontiac", @"GTO", 1969, 2, 45132.2, 370);
        [garage addCar: car];

        car = makeCar (@"Paper Car", @"Plymouth", @"Valiant", 1965, 2, 76800, 105);
        [garage addCar: car];

        [garage print];

        [garage release];
    };

    return (0);

} // main
```

`main()`执行一些簿记工作，创建一个车库，并构建一个小型车队存放在其中。最后，它打印出车库的内容，然后释放它。

运行程序会得到以下激动人心的输出：

```
Joe's Garage:
Herbie, a 1984 Honda CRX, has 2 doors, 110000.0 miles, 58 hp and 4 tires
Badger, a 1987 Acura Integra, has 5 doors, 217036.7 miles, 130 hp and 4 tires
Elvis, a 1989 Acura Legend, has 4 doors, 28123.4 miles, 151 hp and 4 tires
Phoenix, a 1969 Pontiac Firebird, has 2 doors, 85128.3 miles, 345 hp and 4 tires
Streaker, a 1950 Pontiac Silver Streak, has 2 doors, 39100.0 miles, 36 hp and 4 tires
Judge, a 1969 Pontiac GTO, has 2 doors, 45132.2 miles, 370 hp and 4 tires
Paper Car, a 1965 Plymouth Valiant, has 2 doors, 76800.0 miles, 105 hp and 4 tires
```

现在，我们为接下来要介绍的键值便利特性奠定了基础。

顺畅运行



## 关键路径与运算符

关键路径不仅能引用对象值，还可以嵌入运算符来实现诸如获取数组平均值、返回最大值和最小值等操作。

例如，下面展示了如何统计汽车数量：

```
NSNumber *count;
count = [garage valueForKeyPath: @"cars.@count"];
NSLog (@"我们有 %@ 辆车", count);
```

运行后输出：

```
我们有 7 辆车
```

让我们拆解这个关键路径 `"cars.@count"`。`cars` 表示从 `garage` 获取 `cars` 属性，我们知道它是一个 `NSArray`。当然，它实际上是 `NSMutableArray`，但如果不需要修改，我们可将其视为 `NSArray`。接下来的 `@count` 中，@符号预示着某种魔法即将发生。对编译器而言，`@"blah"` 是字符串，`@interface` 是类的声明。在这里，`@count` 告诉 KVC 机制计算关键路径左侧部分的结果数量。

我们还可以对特定值求和，比如车队行驶的总里程。以下代码：

```
NSNumber *sum;
sum = [garage valueForKeyPath: @"cars.@sum.mileage"];
NSLog (@"总里程为 %@ 英里", sum);
```

输出：

```
总里程为 601320.6 英里
```

这个距离足够从地球往返月球，还富余一些里程。

那么这是如何工作的？`@sum` 运算符将关键路径分为两部分。第一部分是通向某个对多关系的路径，此处为 `cars` 数组。第二部分则像中间包含对多关系的普通关键路径，会被应用于关系中的每个对象。因此 `mileage` 会被发送到 `cars` 描述的每个对象，并将结果值相加。当然，这些关键路径都可以是任意长度的。

如果想计算每辆车的平均里程，可以用总和除以数量。但有个更简单的方法——以下代码：

```
NSNumber *avgMileage;
avgMileage = [garage valueForKeyPath: @"cars.@avg.mileage"];
NSLog (@"平均里程为 %.2f", [avgMileage floatValue]);
```

输出：

```
平均里程为 85902.95
```

很简单吧？如果没有这些键值编码的便利功能，我们得写一个循环遍历汽车（假设能获取到 `garage` 的 `cars` 数组），向每辆车询问 `mileage`，累加求和，再除以车辆总数——虽然不难，但仍需编写一小段代码。

这次使用的关键路径是 `"cars.@avg.mileage"`。与 `@sum` 类似，`@avg` 运算符也将关键路径分为两部分：前面部分（此处为 `cars`）是到汽车对多关系的路径，`@avg` 后的部分是另一个关键路径，即 `mileage`。在底层，KVC 会愉快地执行循环、累加值、计数并完成除法运算。

还有 `@min` 和 `@max` 运算符，它们的作用显而易见：

```
NSNumber *min, *max;
min = [garage valueForKeyPath: @"cars.@min.mileage"];
max = [garage valueForKeyPath: @"cars.@max.mileage"];
NSLog (@"最小/最大里程: %@ / %@", min, max);
```

结果如下：

```
最小/最大里程: 28123.4 / 217036.7
```

## KVC 并非免费午餐

KVC 让集合操作变得非常简单。那么为什么不全面使用 KVC，而放弃访问器方法和手写代码呢？天下没有免费的午餐，除非你在某些激进的硅谷科技公司工作。KVC 必然更慢，因为它需要解析字符串来确定你的需求。同时编译器也无法进行错误检查。如果你误写为 `karz.@avg.millage`，编译器无法识别这是个错误的关键路径，运行时才会报错。

有时你会遇到取值有限的属性，比如所有汽车的品牌。即使有百万辆车，品牌种类也很有限。通过关键路径 `"cars.@distinctUnionOfObjects.make"` 可以获取不重复的品牌列表：

```
NSArray *manufacturers;
manufacturers = [garage valueForKeyPath: @"cars.@distinctUnionOfObjects.make"];
NSLog (@"制造商: %@", manufacturers);
```

运行上述代码后输出：

```
制造商: (
    Honda,
    Plymouth,
    Pontiac,
    Acura
)
```

路径中那个名称骇人的运算符 `"@distinctUnionOfObjects"` 名副其实。它与其他运算符逻辑相同：获取左侧指定的集合，对集合中每个对象应用右侧的关键路径，然后将结果值组成新集合。名称中的 "union" 表示取多个对象的并集，"distinct" 表示去除重复项。还有几个类似运算符，留给读者自行探索。另外，你无法自定义运算符，这很遗憾。

## 批量处理人生

KVC 提供两个调用方法支持批量修改对象。第一个是 `dictionaryWithValuesForKeys:`：传入字符串数组，该方法会使用 `valueForKey:` 获取每个键的值，并构建包含键字符串和对应值的字典。

让我们从车库中选一辆车，获取其部分属性的字典：

```
car = [[garage valueForKeyPath: @"cars"] lastObject];
NSArray *keys = [NSArray arrayWithObjects: @"make", @"model",
                 @"modelYear", nil];
NSDictionary *carValues = [car dictionaryWithValuesForKeys: keys];
NSLog (@"汽车属性: %@", carValues);
```

运行后输出 Paper Car 的信息：

```
汽车属性: {
    make = Plymouth;
    model = Valiant;
    modelYear = 1965;
}
```

我们可以修改这些值，将 Valiant 变成一辆（姑且认为）升级版新车——Chevy Nova：

```
NSDictionary *newValues =
    [NSDictionary dictionaryWithObjectsAndKeys:
     @"Chevy", @"make",
     @"Nova", @"model",
     [NSNumber numberWithInt:1964], @"modelYear",
     nil];
[car setValuesForKeysWithDictionary: newValues];
NSLog (@"更新后的车辆信息: %@", car);
```

执行这些代码后，我们看到它确实变成了一辆新车：

```
更新后的车辆信息: Paper Car, 一辆 1964 年款 Chevy Nova, 有 2 个车门, 76800.0 英里里程, 4 个轮胎.
```

注意某些值已更改（`make`、`model` 和年份），但其他值（如名称和里程）保持不变。

这个工具在本程序中并非必不可少，但它能在用户界面代码中实现一些巧妙技巧。例如，你可以实现类似 Apple Aperture 的"吸取与盖印"工具，允许将部分修改（而非全部）从一张图片应用到其他图片。你可以使用 `dictionaryWithValuesForKeys` 提取所有属性，让字典内容驱动用户界面显示。用户可移除字典中的某些项，再使用 `setValuesForKeysWithDictionary` 将修改后的字典应用到其他图片。如果以正确方式设计用户界面类，同一个吸取/盖印面板就能适用于照片、汽车、菜谱等不同对象。

你可能会好奇 `nil` 值如何处理（比如没有名称的车辆），因为字典不能包含 `nil` 值。回想第 7 章，`[NSNull null]` 用于表示 `nil` 值。这里也是如此。对于无名汽车，调用 `dictionaryWithValuesForKeys` 时，`@"name"` 对应的值将是 `[NSNull null]`；反之，你也可以在 `setValuesForKeysWithDictionary` 中传入 `[NSNull null]` 实现相同效果。

## nil 值依然存在

关于 `nil` 值的讨论引出了一个有趣的问题：对于标量值（如里程），"nil" 代表什么？是零？-1？π？Cocoa 无法判断。你可以尝试如下操作：

```
[car setValue: nil forKey: @"mileage"];
```

但 Cocoa 会给出警告：

```
'[<Car 0x105740> setNilValueForKey]: could not set nil as the value for the key mileage.'
```




## 解决此问题

要解决此问题，请重写`*18.01 Car-Value-Garaging.m*`中的`-setNilValueForKey:`方法，并提供合理的逻辑。我们将做出一个执行决策：`nil`里程数意味着将汽车里程数归零，而不是使用像`-1`这样的其他值：

```
- (void) setNilValueForKey: (NSString *) key
{
    if ([key isEqualToString: @"mileage"])
    {
        mileage = 0;
    } else {
        [super setNilValueForKey: key];
    }
} // setNilValueForKey
```

请注意，如果我们遇到意外的键，我们会调用父类方法。这样，如果有人试图对我们不理解的键使用键值编码，调用者将收到适当的抱怨。通常，除非有罕见且充分的理由不这样做（例如故意避免某个特定操作），否则在重写时始终调用父类方法。

## 处理未处理的键

我们键值之旅（三小时之旅？）的最后一站是处理未定义的键。如果你曾尝试过 KVC 并输错了键，你可能会看到这样的信息：

```
'[<Car 0x105740> valueForUndefinedKey:]: this class is not key value coding-compliant for the key garbanzo.'
```

这基本上是说 Cocoa 无法理解你使用该键的含义，因此它放弃了。

如果你仔细观察错误消息，你会发现它提到了`valueForUndefinedKey:`方法。正如你可能猜到的，我们可以通过重写此方法来处理未定义的键。你还可以猜到，有一个对应的`setValue:forUndefinedKey:`方法，用于当你尝试使用未知键更改值时。

如果 KVC 机制无法找到执行其魔法的途径，它会回退并询问该类该怎么做。默认实现只是像你之前看到的那样“摊手”。不过，我们可以改变这种行为。让我们将`Garage`变成一个非常灵活的对象，允许我们设置和获取任何键。我们首先添加一个可变字典：

```
@interface Garage : NSObject
{
    NSString *name;
    NSMutableArray *cars;
    NSMutableDictionary *stuff;
}
… @end // Garage
```

接下来，我们添加`valueForUndefinedKey:`方法：

```
- (void) setValue: (id) value forUndefinedKey: (NSString *) key
{
    if (stuff == nil)
    {
        stuff = [[NSMutableDictionary alloc] init];
    }
    [stuff setValue: value forKey: key];
} // setValueForUndefinedKey

- (id) valueForUndefinedKey:(NSString *)key
{
    id value = [stuff valueForKey: key];
    return (value);
} // valueForUndefinedKey
```

并在`-dealloc`中释放字典。

现在，你可以通过向`*18.01 Car-Value-Garaging.m*`添加代码，在车库上设置任意值：

```
[garage setValue: @"bunny" forKey: @"fluffy"];
[garage setValue: @"greeble" forKey: @"bork"];
[garage setValue: [NSNull null] forKey: @"snorgle"];
[garage setValue: nil forKey: @"gronk"];
```

并获取它们：

```
NSLog (@"values are %@ %@ %@ and %@", [garage valueForKey: @"fluffy"], [garage valueForKey: @"bork"], [garage valueForKey: @"snorgle"], [garage valueForKey: @"gronk"]);
```

这个`NSLog`会打印出：

```
values are bunny greeble <null> and (null)
```

注意`<null>`和`(null)`之间的区别。`<null>`是一个`[NSNull null]`对象，而`(null)`是一个真正的`nil`值，我们得到它是因为`"gronk"`不在字典中。还要注意，我们在使用`stuff`字典时使用了 KVC 方法`setValue:forKey:`。使用此方法允许调用者传入`nil`值，而无需我们在代码中检查它。`NSDictionary`的`setObject:forKey:`在传入`nil`时会报错，而`setValue:forKey:`在字典上使用`nil`值则会从字典中移除该键。

## 总结

键值编码包含的内容远多于我们在本章所能覆盖的，但现在你应该有一个坚实的基础来探索 KVC 的其他方面。你已经看到了使用单个键设置和获取值的示例，其中 KVC 会查找 setter 和 getter 方法来完成你的请求。如果找不到任何方法，KVC 会直接深入对象内部并更改值。

你还了解了键路径，它们是点分隔的键，指定了通过对象网络的路径。这些键路径可能看起来很像在访问属性，但实际上它们是截然不同的机制。你可以在键路径中插入各种运算符，让 KVC 为你执行额外的工作。最后，我们研究了几个你可以重写以自定义边界情况行为的方法。

接下来，我们将了解 Xcode 的静态分析器。

