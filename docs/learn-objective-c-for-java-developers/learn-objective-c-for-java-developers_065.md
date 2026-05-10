# 第 18 章 — 提供者/订阅者模式

提供者/订阅者模式定义了一种从提供者对象到任意数量订阅者对象的一对多、单向信息流。其概念是提供者拥有有用的信息或事件，需要传递给其他对象（即它的订阅者），这些订阅者将利用这些信息来执行额外的操作或与提供者保持同步。用 Java 的术语来说，提供者向其监听器触发通知。用 Objective-C 的术语来说，提供者向其观察者发布通知。

提供者/订阅者模式在 Java 中被大量使用，但 Java 没有提供任何内在的服务来帮助对象管理其订阅者或协调消息。因此，大多数 Java 对象实现了自己的订阅者管理。这并非特别困难，但它确实带来一个重要的限制：提供者和订阅者必须持有彼此的引用。Objective-C 通过提供系统范围的服务来管理提供者的订阅者并向他们传递通知，从而大力提倡提供者/订阅者模式。通过提供中间管理层，提供者和观察者（订阅者）可能彼此完全了解，也可能几乎一无所知。



本章首先展示典型的 Java 监听器接口如何通过 Cocoa 的通知服务来实现。随后将描述其他可能的提供者/订阅者关系，以及如何在设计中运用这些关系。最后，将介绍分布式通知（进程间传递的通知）以及线程安全性等相关考量。你还需要阅读下一章关于观察者模式的内容，因为这两种模式非常相似。

### 通知

在 Objective-C 中，发送给观察者的通知被封装在`NSNotification`实例中。提供者决定`NSNotification`对象的内容，接收者则决定它将要接收的 Objective-C 消息。

列表 18-1 和 18-2 对比了 Java 监听器与 Objective-C 观察者之间的差异。列表 18-1 定义了一个监控温度的`Thermometer`类。每当温度发生变化时，它就会通知其观察者。

### 列表 18-1. 温度计，温度提供者

**Java**

```java
public interface TemperatureListener
{
    public void temperatureChanged( Thermometer thermometer, float temperature );
}
```

```java
public class Thermometer
{
    private float temperature;
    private HashSet<TemperatureListener> listeners =
        new HashSet<TemperatureListener>();

    public float getTemperature( )
    {
        return temperature;
    }

    public void setTemperature( float newTemp )
    {
        if (temperature!=newTemp) {
            temperature = newTemp;
            fireTemperatureChange();
        }
    }

    public void addListener( TemperatureListener listener )
    {
        listeners.add(listener);
    }

    public void removeListener( TemperatureListener listener )
    {
        listeners.remove(listener);
    }

    public void fireTemperatureChange( )
    {
        for ( TemperatureListener listener : listeners ) {
            listener.temperatureChanged(this,temperature);
        }
    }
}
```

**Objective-C**

```objc
#define TempDidChange @"TemperatureDidChange"

@interface Thermometer : NSObject {
    float temperature;
}
@property float temperature;
@end

@implementation Thermometer

- (float)temperature
{
    return temperature;
}

- (void)setTemperature:(float)newTemp
{
    if (temperature!=newTemp) {
        temperature = newTemp;
        NSNumber *tempValue = [NSNumber numberWithFloat:temperature];
        NSDictionary *info = [NSDictionary dictionaryWithObject:tempValue forKey:@"Temperature"];
        [[NSNotificationCenter defaultCenter] postNotificationName:TempDidChange object:self
                                                          userInfo:info];
    }
}

@end
```

列表 18-2 展示了一个温度观察者，它接收来自`Thermometer`对象的温度变化通知。该观察者通过待观察的`Thermometer`对象进行初始化，并记录温度值的任何变化。

### 列表 18-2. 温度监视器，温度观察者

**Java**

```java
public class TemperatureMonitor implements TemperatureListener
{
    public TemperatureMonitor( Thermometer thermometer )
    {
        thermometer.addListener(this);
    }

    public void temperatureChanged( Thermometer thermometer, float temperature )
    {
        System.out.println("Temperature is now "+temperature);
    }
}
```

**Objective-C**

```objc
#import "Thermometer.h"

@interface TemperatureMonitor : NSObject
- (id)initWithThermometer:(Thermometer*)thermometer;
- (void)tempChanged:(NSNotification*)notification;
@end

@implementation TemperatureMonitor

- (id)initWithThermometer:(Thermometer*)thermometer
{
    self = [super init];
    if (self != nil) {
        [[NSNotificationCenter defaultCenter] addObserver:self
                                                 selector:@selector(tempChanged:)
                                                     name:TempDidChange
                                                   object:thermometer];
    }
    return self;
}

- (void)tempChanged:(NSNotification*)notification
{
    id tempValue = [[notification userInfo] objectForKey:@"Temperature"];
    NSLog(@"Temperature is now %@",tempValue);
}

@end
```

列表 18-1 和 18-2 中的 Java 与 Objective-C 解决方案在功能上是等效的。具体来说：

- `Thermometer`类定义了它将发送的通知。
- 每当温度发生变化时，`Thermometer`类都会通知其观察者。



• 一个`TemperatureMonitor`对象仅接收其正在观察的单个`Thermometer`对象发出的温度变化通知。
• 一条通知包含新的温度值以及一个指向发出该通知的`Thermometer`对象的引用。
• 通知以同步方法调用的方式传递。也就是说，所有通知方法都会在`setTemperature`返回之前执行完毕。

显著差异如下：

