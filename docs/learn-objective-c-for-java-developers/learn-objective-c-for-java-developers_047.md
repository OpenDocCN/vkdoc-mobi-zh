# 第 13 章 ■ 近程与远程通信

##### 分布式对象

分布式对象（DO）是对象间通信的巅峰，等同于 Java 的远程方法调用（RMI）技术。分布式对象比 RMI 更灵活、更易用，使其成为解决多种设计问题的便捷方案。本节将介绍分布式对象的基本工作原理、建立与远程对象连接的几种不同方式、如何发送和接收消息，以及如何影响进程间的对象交换。

### 分布式对象的工作原理

在 Java 中使用远程方法调用通常涉及以下步骤：

1. 创建一个接口，定义你的远程对象所实现的方法。
2. 创建一个类，实现步骤 1 中的接口。
3. 执行 `rmic` 工具，根据实现类生成 `_Stub` 类。
4. 将所有传递给接口方法或从接口方法返回的对象设置为 `Serializable`。
5. 启动 `rmiregistry` 进程。
6. 在你的服务器进程中创建实现类的一个实例。


7.  将该对象注册为命名服务。

8.  在客户端进程中，请求该服务的代理对象。

9.  调用代理对象的方法，以在服务器进程中的对象实例上调用相同的方法。

在 Objective-C 中使用分布式对象遵循相同的一般工作流程，但 Objective-C 省略了步骤 3 和 5，并使步骤 1、2、4 和 7 变为可选。在 Objective-C 中，使用分布式对象所需的最小步骤如下：

1.  通过附加到运行循环的连接对象提供任意对象。
2.  客户端从连接请求所提供对象的代理对象。
3.  客户端向代理对象发送消息。

关键区别在于，你无需做任何特殊操作即可创建远程对象的代理——Objective-C 会自动创建代理对象——并且对象无需可归档即可传递给远程消息或从远程消息返回。尽管使对象可归档可能带来一些优势，如“按副本传递对象”一节所述。

清单 13-4 对比了 Java RMI 与 Objective-C 分布式对象。这些演示的完整源代码和项目可在 `http://www.apress.com/` 的“源代码/下载”区域获取。其中包含了用于编译和运行示例的 Shell 脚本。要逐个尝试 Objective-C 示例，请打开并构建 Xcode 项目。打开一个终端窗口，`cd` 到包含 `Greeter` 和 `Guest` 可执行文件的构建目录。使用 `./Greeter` 或 `./Guest` 从 Shell 运行命令。你可能希望打开多个窗口来测试进程间通信，或者将可执行文件复制到同一网络上的另一台计算机，以试验网络连接。按 `Control-C` 停止正在运行的服务器进程。

Java 实现在功能上与 Objective-C 版本类似。两个示例都在两个独立的进程中运行：一个 `Greeter` 服务器和一个 `Guest` 客户端。`Greeter` 进程创建并发布一个单一的 `Greeter` 对象。客户端应用程序通过按名称查找 `Greeter` 服务来连接到远程进程，获取服务器中存在的 `Greeter` 对象的代理，并与其进行交互。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 13 章 ■ 远近通信

这些程序的输出如清单 13-5 所示。服务器和客户端形成一对多的关系。你可以根据需要启动任意数量的客户端进程；它们都将连接并与单个 `Greeter` 对象进行交互。

清单 13-4. 远程方法调用

Java：`Greeter` 和 `Guest` 类

```java
public interface Greeter extends Remote {

    public void sayHello( ) throws java.rmi.RemoteException;

    public void greetGuest( Guest listener ) throws java.rmi.RemoteException; 
    public String sayGoodbye( ) throws java.rmi.RemoteException;

}

public class GreeterImpl extends UnicastRemoteObject implements Greeter {

    private static final long serialVersionUID = 999010092613539924L; 
    public static void main(String[] args)

    {

        String greeterServiceURI = makeServiceURI(null,null);

        try {

            Greeter greeter = new GreeterImpl();

            System.out.println("Starting Greeter service at "+greeterServiceURI); 
            Naming.rebind(greeterServiceURI,greeter);

        } catch (Exception e) {

            e.printStackTrace();

        }

    }

    public static String makeServiceURI( String host, String name )

    {

        if (host==null)

            host = "localhost";

        if (name==null)

            name = "JavaGreeter";

        return "rmi://"+host+"/"+name;

    }

    public GreeterImpl() throws RemoteException

    {

        super();

    }

    public void sayHello() throws RemoteException

    {

        System.out.println("Greeter "+getClass().getName()

            +" was asked to sayHello()");

    }
```

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 13 章 ■ 远近通信

```java
    public void greetGuest(Guest guest) throws RemoteException

    {

        System.out.println("Greeter "+getClass().getName()

            +" was asked to greetGuest("+guest+")"); 
        guest.listen("I'm pleased to meet you, "+guest+"!");

    }

    public String sayGoodbye() throws RemoteException

    {

        System.out.println("Greeter "+getClass().getName()

    }
```


```java
public class GreeterImpl implements Greeter {
    private static final long serialVersionUID = -478469725382736366L;

    public static String makeServiceURI(String host, String port) {
        return "rmi://localhost/JavaGreeter";
    }

    public void sayHello() {
        System.out.println("Greeter " + this + " was asked to sayHello()");
    }

    public void talkBackTo(Guest guest) {
        System.out.println("Greeter " + this + " was asked to talkBackTo(" + guest + ")");
        guest.listen("I'm pleased to meet you, " + guest + "!");
    }

    public String sayGoodbye() {
        System.out.println("Greeter " + this + " was asked to sayGoodbye()");
        return "It was a pleasure serving you.";
    }
}

public class Guest implements Serializable {
    private static final long serialVersionUID = -478469725382736366L;

    public static void main(String[] args) {
        String greeterServiceURI = GreeterImpl.makeServiceURI(null, null);
        try {
            System.out.println("Looking up greeter at " + greeterServiceURI);
            Greeter greeter = (Greeter) Naming.lookup(greeterServiceURI);
            Guest guest = new Guest();
            greeter.sayHello();
            greeter.greetGuest(guest);
            String lastWord = greeter.sayGoodbye();
            System.out.println("Greeter's final response was \"" + lastWord + "\"");
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    public void listen(String message) {
        System.out.println(getClass().getName() + " heard \"" + message + "\"");
    }
}
```

## Objective-C：迎宾程序

```objectivec
@class Guest;

@interface Greeter : NSObject
- (void)sayHello;
- (void)greetGuest:(Guest*)guest;
- (NSString*)sayGoodbye;
@end

@implementation Greeter
- (void)sayHello {
    NSLog(@"迎宾器 %@ 被请求执行 sayHello", self);
}

- (void)greetGuest:(bycopy Guest*)guest {
    NSLog(@"迎宾器 %@ 被请求执行 greetGuest:%@", self, guest);
    [guest listen:[NSString stringWithFormat:@"很高兴见到你，%@！", guest]];
}

- (NSString*)sayGoodbye {
    NSLog(@"迎宾器 %@ 被请求执行 sayGoodbye", self);
    return @"很荣幸为您服务。";
}
@end

int main(int argc, const char * argv[]) {
    NSConnection *connection = [NSConnection defaultConnection];
    [connection setRootObject:[Greeter new]];
    if ([connection registerName:SERVICE_NAME_DEFAULT]) {
        NSLog(@"正在启动迎宾服务 '%@'", name);
        [[NSRunLoop currentRunLoop] run]; // 永远不会返回
    }
    return 0;
}
```

## Objective-C：访客程序

```objectivec
@interface Guest : NSObject
- (void)listen:(NSString*)message;
@end

@implementation Guest
- (void)listen:(NSString*)message {
    NSLog(@"%@ 听到 \"%@\"", self, message);
}
@end

int main(int argc, const char * argv[]) {
    NSConnection *connection = nil;
    NSLog(@"正在通过 Mach 端口连接到迎宾器 '%@'", name);
    connection = [NSConnection connectionWithRegisteredName:SERVICE_NAME_DEFAULT host:nil];
    Greeter *greeter = (Greeter*)[connection rootProxy];
    Guest *guest = [Guest new];
    [greeter sayHello];
    [greeter greetGuest:guest];
    NSString *lastWord = [greeter sayGoodbye];
    NSLog(@"迎宾器的最终回应是 \"%@\"", lastWord);
    return 0;
}
```

## 清单 13-5. 迎宾演示的输出

**Java GreeterImpl：**
```
$ java com.apress.java.rmi.GreeterImpl
正在启动迎宾服务，地址为 rmi://localhost/JavaGreeter
迎宾器 com.apress.java.rmi.GreeterImpl 被请求执行 sayHello()
迎宾器 com.apress.java.rmi.GreeterImpl 被请求执行 talkBackTo(Guest@e9cb75)
com.apress.java.rmi.Guest 听到 "很高兴见到你，Guest@e9cb75！"
迎宾器 com.apress.java.rmi.GreeterImpl 被请求执行 sayGoodbye()
```

**Java Guest：**
```
$ java com.apress.java.rmi.Guest
正在查找位于 rmi://localhost/JavaGreeter 的迎宾器
迎宾器的最终回应是 "很荣幸为您服务。"
```

**Objective-C 迎宾器：**
```
$ ./Greeter --mach
正在启动迎宾服务 'ObjCGreeter'
迎宾器 <Greeter: 0x1011bf0> 被请求执行 sayHello
迎宾器 <Greeter: 0x1011bf0> 被请求执行 greetGuest:<Guest: 0x1014bc0>
迎宾器 <Greeter: 0x1011bf0> 被请求执行 sayGoodbye
```

**Objective-C 访客：**
```
$ ./Guest --mach
正在通过 Mach 端口连接到迎宾器 'ObjCGreeter'
<Guest: 0x1014bc0> 听到 "很高兴见到你，<Guest: 0x1014bc0>！"
迎宾器的最终回应是 "很荣幸为您服务。"
```

Java 和 Objective-C 的远程方法调用在概念上非常相似：服务器进程实例化一个实现服务的对象。一个或多个客户端进程连接到该服务并获取一个代理对象。代理对象并不实现服务器的任何代码。相反，代理会通过连接将任何方法调用转发到服务器进程，在那里实际执行所需的方法。任何参数和返回值也通过类似的方式进行编码并通过连接传输。

Java 和 Objective-C 的实现之间存在两个显著差异。第一个差异在于代理对象的创建方式。在 Java 中，你必须首先设计一个接口来定义服务器对象的方法，以及一个实现这些方法的类。然后，Java 的 `rmic` 编译器会创建一个适合用作代理对象的特殊 `_Stub` 类。客户端进程获取该代理对象并调用其方法，这些方法会被转发回服务器执行。

Objective-C 提供了 `NSProxy` 类。这是一个非常特殊的类，因为它不是 `NSObject` 的子类。`NSObject` 和 `NSProxy` 都是根类 `Object` 的子类。你所认为的大多数基类方法都是在 `NSObject` 中定义的，而不是 `Object`。因此，`NSProxy` 不继承任何方法——这使其非常适合其用途。

`NSProxy` 重写了第 6 章中描述的 `-forwardInvocation:` 方法。由于它几乎不实现任何方法，实际上你发送给它的任何方法最终都会调用 `-forwardInvocation:`。`NSDistantObject` 是 `NSProxy` 的一个可用子类，它将 `NSProxy` 与 `NSConnection` 连接起来。任何发送给代理对象的消息都会被归档并通过 `NSConnection` 传输，由远程进程执行。通过利用 Objective-C 的未实现方法处理机制，可以针对任何 Objective-C 对象动态创建一个轻量级的代理对象。

这意味着你可以通过 `NSConnection` 共享几乎任何对象。

> **警告**：`NSProxy` 对象并不包含其所代表对象的任何实例变量。为方便起见，代理对象指针常常被强制转换为实际对象类型的指针。注意不要使用指向代理的指针直接访问对象的任何实例变量，例如 `distantObject->value`。这几乎肯定会造成灾难性后果。请改用属性访问器（`[distantObject value]` 或 `distantObject.value`），因为这些访问器会被转换为消息；或者使用对象标识符类型（`id`），它本质上禁止了直接变量访问。在设计分布式对象时，请进行防御性编程。将所有实例变量设为 `@protected` 或 `@private`，并为所有公共属性提供访问器方法。

另一个显著差异在于参数和返回值的交换方式。在 Java 中，所有参数和返回值通过副本传递，因此必须可序列化。这也意味着实现这些值的类必须存在于服务器中。在清单 13-5 的示例输出中，找到 Java 和 Objective-C 中“Guest heard ‘I’m pleased to meet you, Guest!’”这一消息。在 Java 版本中，该消息由服务器进程发出。客户端语句 `greeter.greetGuest(guest)` 将 `Guest` 对象的副本序列化并发送给服务器。当 `Greeter` 执行 `guest.listen("I'm pleased to meet you, guest!")` 时，`listen(…)` 方法在服务器本地 `guest` 对象的副本上执行。

在 Objective-C 的输出中，“Guest heard ‘I’m pleased to meet you, Guest!’”这一消息由客户端发出。这是因为 Objective-C 的默认行为是按引用传递值。`[greeter greetGuest:guest]` 语句将 `guest` 对象的引用传递给了服务器。服务器的 `-greetGuest:` 方法接收到一个指向客户端中 `Guest` 实例的代理 `NSDistantObject`。当服务器发送消息 `[guest listen:@"I'm pleased to meet you, guest!"]` 时，消息调用被截获、归档并发送回客户端执行，从而在客户端输出了该消息。


这使得你几乎可以将任何对象传递给远程方法。该对象无需是可归档的，你只需注意接收方会获得一个原始对象的代理，该代理由`NSDistantObject`自动创建。如果需要按拷贝传递对象，你可以让对象遵循`NSCoding`协议并实现序列化归档，如第 12 章所述。

你的对象还必须实现代码，以决定在何种情况下应通过拷贝或引用传递。所有这些内容都在“通过拷贝传递对象”部分中解释。

## 建立连接

使用分布式对象有点像约会；你必须先找到合适的伴侣才能开始对话。你可以使用第三方媒人服务，或者如果你对对方的存在有第一手了解，也可以自己操作。

Java 的 `java.rmi.Naming` 类扮演了媒人的角色，允许服务器进程发布其存在。对于客户端而言，它定位所需的服务器进程并返回一个代理对象。

通过该代理对象，客户端与服务器建立连接。

在 Objective-C 中，`NSConnection` 是核心角色，通过分布式对象连接两个进程。`NSConnection` 本身并不提供注册服务——尽管它为常用服务提供了便捷构造器。要建立服务器与客户端之间的连接，你必须创建一个使用两个单向 `NSPort` 对象或一个双向 `NSPort` 对象的 `NSConnection` 对象。`NSConnection` 使用 `NSPort` 对象向远程进程传输数据和调用信息。总体安排如图 13-1 所示。实线箭头表示进程内的对象引用。空心箭头表示通过某个端口或套接字交换数据的方向。

**图 13-1.** `NSConnection` 中的对象

如何创建 `NSConnection` 对象取决于你想要创建的连接类型。你可以直接创建 `NSPort` 对象，或从注册服务获取已连接的 `NSPort` 对象，然后使用它们来创建 `NSConnection`。此外，还有一些工具可以为你创建预配置的 `NSConnection` 对象。表 13-2 列出了四种常见的 `NSConnection` 配置。

**表 13-2.** 常见的 `NSConnection` 配置

| 连接类型 | 如何创建 |
| --- | --- |
| 同一进程中的两个对象 | 创建两个 `NSPort` 对象，然后使用它们创建 `NSConnection`。 |
| 通过 Mach 端口的连接 | 让 `+[NSConnection connectionWithRegisteredName:host:]` 为你创建预配置的 `NSConnection`。 |
| 通过 IP 套接字的连接 | 从 `NSSocketPortNameServer` 获取一个 `NSPort`，并用它创建 `NSConnection`。 |

媒人的角色通常由某个 `NSPortNameServer` 子类扮演。每个名称服务器为特定类别的端口提供注册和发现服务。

`NSMachBootstrapServer` 注册 Mach 消息端口。Mach 端口的作用域限于当前进程的引导命名空间。`NSSocketPortNameServer` 在局域网中注册 TCP 端口。通过 `NSSocketPortNameServer` 注册的服务，本地网络上的任何计算机都可以访问。

一旦 `NSConnection` 对象创建完成，服务器对象便被提供。这使得该对象对任何连接到服务器 `NSConnection` 的客户端可用。这是通过 `-[NSConnection setRootObject:]` 实现的。设置后，客户端向其 `NSConnection` 发送 `-rootProxy` 消息，以获取服务器根对象的 `NSDistantObject`。客户端和服务器需要交换的任何其他对象，都是通过向服务器根对象发送消息来完成的。服务器可以随时更改其 `rootObject`，但这不会影响客户端先前获得的任何代理对象。



### 该演示项目包含四种常见连接方式的示例。你可以通过命令的`mode`参数进行尝试。每种方式的说明如下。

## 线程间连接

分布式对象的重点是进程间通信，但 DO 同样适用于同一进程内的线程间通信。使用 DO，可以相对轻松地创建一个在其自身线程中接收并执行消息的类，从而使功能强大的多线程服务像简单对象一样易于使用。它还为远程服务提供了灵活的部署环境。一个设计用于在远程计算机上运行的服务，同样可以轻松地在同一台机器的独立进程中运行，或者作为测试时的本地进程中的线程运行。客户端对所有这三种情况的接口将是相同的。

创建线程间连接很简单。创建两个通用的`NSPorts`来提供通信，并用它们来创建`NSConnection`对象，如代码清单 13-6 所示。要在演示项目中运行此示例，请执行命令`./Guest --thread`。

### 代码清单 13-6. 线程间 DO 连接

```
NSPort *receivePort = [NSPort port];
NSPort *sendPort = [NSPort port];
NSConnection *clientConnection;
NSConnection *serverConnection;

clientConnection = [NSConnection connectionWithReceivePort:receivePort sendPort:sendPort];
serverConnection = [NSConnection connectionWithReceivePort:sendPort sendPort:receivePort];

[serverConnection setRootObject:[Server new]];
[serverConnection runInNewThread];
…
id server = [clientConnection rootProxy];
[server doSomething];
```

请注意，在`serverConnection`中端口顺序是相反的。客户端的接收端口连接到服务器的发送端口，反之亦然。`-runInNewThread`方法会分离出一个新线程，该线程运行自己的运行循环，并连接到连接上。这段代码有很多变体。有些程序员更喜欢创建自己的线程，将`NSPort`对象传递给它，然后让服务器对象创建其`NSConnection`。只要最终结果相同，采用哪种方式都无关紧要。

## Mach 端口连接

Mach 端口在单个用户范围内或与系统进程通信时，能提供极其高效的进程间通信。`NSMachPortNameServer`通过名称注册服务器进程，任何客户端都可以轻松连接到它。客户端和服务器的代码如代码清单 13-7 所示。你可以在不同的终端窗口中执行演示命令`./Greeter --mach`和`./Guest --mach`来亲自尝试。

### 代码清单 13-7. 通过 Mach 端口的 DO 连接

**服务器端**

```
NSConnection *connection = [NSConnection defaultConnection];
[connection setRootObject:[Server new]];
if ([connection registerName:@"Server"])
    [[NSRunLoop currentRunLoop] run];
```

**客户端**

```
NSConnection *connection = [NSConnection connectionWithRegisteredName:@"Server"
    host:nil];
id server = [connection rootProxy];
[server doSomething];
```

服务器创建其服务器对象，并用其公开名称进行注册。客户端使用便捷构造器`-connectionWithRegisteredName:host:`来获取一个`NSConnection`，该连接已预先配置了连接到注册名称为`@"Server"`的 Mach 端口的`NSPorts`。

服务器和客户端也有可能运行在同一进程的两个线程中。通过`NSMachBootstrapServer`注册的服务，其他进程可以访问，但同样也可以很容易地通过另一个线程进行连接。

## 网络连接

当用于通过网络与对象交互时，分布式对象的灵活性尤为突出。`NSSocketPortNameServer`提供注册和发现服务，本地局域网中的所有系统均可访问，如代码清单 13-8 所示。`./Greeter --network`和`./Guest --network`演示可以在同一台计算机上运行，也可以在同一网络的多台计算机上运行。



### 列表 13-8. 通过本地网络的分部对象连接

**服务器**

`NSSocketPort *port = [NSSocketPort new];`
`NSConnection *connection = [NSConnection connectionWithReceivePort:port sendPort:nil];`
`[connection setRootObject:[Server new]];`
`if ([[NSSocketPortNameServer sharedInstance] registerPort:port name:@"Server"])`
`    [[NSRunLoop currentRunLoop] run];`

**客户端**

`NSPort *port = [[NSSocketPortNameServer sharedInstance] portForName:@"Server" host:@"*"];`
`NSConnection *connection = [NSConnection connectionWithReceivePort:nil sendPort:port];`
`id server = [connection rootProxy];`
`[server doSomething];`

网络版本只是对前述主题的细微变体。在此，服务器和客户端直接与 `NSSocketPortNameServer` 交互，以直接注册和获取 `NSSocketPort` 对象。然后，这些端口被用于创建 `NSConnection` 对象。

如果主机参数为 `@"*"`，则 `NSSocketPortNameServer` 会在本地网络上定位任何已注册 `@"Server"` 服务的计算机。如果你希望连接到特定目标机器，主机参数也可以指定为具体的域名或 IP 地址。

### BSD 管道连接

最后一个例子稍微有点特殊。它演示了如何通过文件系统中的命名 BSD 管道“文件”来连接两个进程。管道文件看起来像数据文件，但实际上它是两个进程之间的实时缓冲区。写入该“文件”的字节会立即作为数据出现在另一个进程中，但永远不会写入任何存储设备。这展示了分布式对象的灵活性。本质上，任何能够连接到 `NSPort` 的通信管道都可以用来创建 `NSConnection`。由于你可以创建 `NSPort` 的自定义子类，因此可能性是无限的。

演示该技术的项目命令是 `./Greeter --socket` 和 `./Guest --socket`。相关代码位于 `Greeter_main.m` 和 `Guest_main.m` 文件中。

[www.it-ebooks.info](http://www.it-ebooks.info/)

## 第 13 章 远近通信

### 运行循环

运行循环是驱动分布式对象的引擎。第 15 章会更详细地描述运行循环。

在上述服务器示例中，最后一步是启动一个运行循环，用于处理其 `NSPort` 对象接收到的事件。为了接收和分发异步消息，`NSConnection` 必须由运行循环驱动。客户端发送的消息被推送到服务器的 `NSPort` 中。运行循环逐一弹出这些消息，并将它们分发给根对象。

很明显，服务器必须使用运行循环。不太明显的是，客户端也在运行一个运行循环。当客户端发送同步消息（即 `[server doSomething]`）时，它会将消息推送到 `NSPort`，然后启动一个临时运行循环来等待响应。服务器接收消息并通过向客户端发送回复消息来响应。客户端的临时运行循环从其 `NSPort` 中弹出回复，并将控制权返回给发送方。

请再次查看列表 13-4 中 Greeter 和 Guest 示例的代码。假设 Greeter 保存了对代理 Guest 对象的引用，并在 `-greetGuest:` 返回后使用该引用发送另一条 `-listen:` 消息。如果客户端进程没有运行运行循环，那么 `-listen:` 消息将永远停留在客户端的 `NSPort` 中而不会被执行。由于该方法是同步的，Greeter 对象将会挂起，等待 Guest 对象响应——或者至少会一直等到 `NSConnection` 超时。

为了实现分布式对象之间的异步双向消息传递，两个进程都必须拥有活跃的运行循环。

### 异步消息

如前所述，通过 `NSProxy` 发送的消息可以是同步或异步的。要创建异步消息，请在方法接口的返回值前添加 `oneway` 关键字。

```objc
- (oneway void)doSomething;
```

该消息将被发送到远程对象，但执行会立即返回到发送方。`oneway` 方法应始终具有 `void` 返回类型。如“运行循环”一节所述...



### 节

如果客户端期望从服务器接收任何异步回复，则必须运行自己的运行循环。`oneway` 修饰符以及稍后介绍的其他参数修饰符，仅影响通过`NSProxy`对象发送的消息。常规 Objective-C 消息（即`[object doSomething]`）的行为不会改变。

### 按副本传递对象

在参数中传递或从消息返回的对象，可以通过副本或引用与远程对象交换。通过引用发送对象需要为该对象创建一个`NSDistantObject`代理，并将其发送给接收方。

要按副本传递对象，需要使用顺序归档技术对该对象进行归档，然后在接收端重构。这给该类提出了三个要求。首先，该类必须遵循`NSCoding`协议并实现顺序归档，如序列化章节所述。其次，该类的代码必须存在于接收进程中——否则，就无法实例化该对象。

最后，该类必须重写其`-replacementObjectForPortCoder:`方法，以决定何时应复制该对象，何时应通过引用传递。Objective-C 可以通过`bycopy`和`byref`修饰符向该类提供提示。这些类型修饰符会增强方法实现中的对象参数和返回值，如下所示：

```
- (bycopy ProjectStatus*)statusForProject:(byref Project*)project { …
```

### 演示项目

演示项目包含了`Guest`对象的`-replacementObjectForPortCoder:`方法的实现，如代码清单 13-9 所示。

**代码清单 13-9.** Guest 类的 `bycopy` 实现

```
- (id)replacementObjectForPortCoder:(NSPortCoder*)coder
{
    if ([coder isBycopy])
        return self;
    return [super replacementObjectForPortCoder:coder];
}
```

从分布式对象的角度来看，所有对象都是按副本传递的。对象的归档副本是使用顺序归档生成的。如你在第 12 章中所学，`NSPortCoder`编码器首先会调用`-replacementObjectForPortCoder:`，以允许对象在归档中替换为另一个不同的对象。`NSObject`的默认实现会为该对象替换为一个`NSDistantObject`。

分布式对象框架不执行将原始对象替换为代理对象的操作；每个对象自行决定是复制自身还是提供一个代理对象。

代码清单 13-9 中的代码根据方法定义中嵌入的提示，为`Guest`对象做出了按副本传递的决策。如果参数或返回值类型包含了`bycopy`关键字，那么用于归档该对象的`NSPortCoder`在收到`-isBycopy`消息时会返回`YES`。如果指定了`byref`修饰符或未指定任何修饰符，`-isBycopy`将返回`NO`。或者你也可以测试`-isByref`，它始终返回`!isBycopy`。

或者，你可以选择忽略编码器的建议，返回`self`、`[super replacmentObjectForPortCoder:coder]`或任何其他功能等效的替代对象。某些对象，如`NSString`，选择始终按副本发送自身，忽略任何`byref`提示。默认情况下，`NSObject`的子类始终通过引用发送自身，即使方法请求了`bycopy`。你的代码可以使用任何标准来做出决策。如果`NSPortCoder`通过网络连接到某个服务，对象可能会按副本发送自身；但如果连接到同一系统中的另一个进程，则可能会选择发送引用。

### 传递指针

将 C 指针作为参数传递带来了另一个问题。指针不能按值复制——因为本地地址对接收进程毫无意义。也无法为远程结构创建等效的代理对象。分布式对象通过复制指针参数所指向的结构的值来解决这个问题——除非指针是`NULL`，在这种情况下，它只会向接收者发送`NULL`。

```
- (void)rectToLocalCoordinates:(NSRect*)rectangle;
```

当`-rectToLocalCoordinates:`消息被发送给一个远程对象时，`NSRect`结构的内容会被逐字复制到远程进程，接收方获得的 C 指针指向由分布式对象分配的本地副本。当方法返回时，`NSRect`结构（可能已修改）的内容会被复制回发送方，临时副本则被销毁。

这种方式效率不算特别高，但很安全。对于仅引用结构内容或忽略其原始内容的方法，Objective-C 提供了表 13-3 中列出的参数修饰符。

**表 13-3.** 指针参数类型修饰符

| 修饰符 | 效果 |
|---|---|
| `in` | 结构会被复制到接收方，但不会复制回发送方。 |
| `out` | 结构不会被复制到接收方，但修改后的结构会在返回时复制回发送方。 |
| `inout` | 结构会被复制到接收方，然后在方法返回时再复制回来。此为默认行为。 |

对于向接收方传递信息、且接收方不使用指针修改结构内容的参数，请使用`in`修饰符。示例方法如下：

```
- (BOOL)isValidPoint:(in NSPoint*)inPoint;
```

对于用于指向一个未初始化结构、并由接收方填充其内容的参数，请使用`out`修饰符。示例方法如下：

```
- (void)getSize:(out NSSize*)outSize;
```

`inout`修饰符是默认行为。包含它不会改变方法的行为，但会使你的意图更清晰。

> **警告** 结构通过副本发送给接收方。结构只能包含原始值。显而易见，它不能包含指向其他结构或对象的指针。如果结构如此复杂，则需将其重新设计为对象，或先转换为某种可移植格式，然后才能与分布式对象一起使用。

指针值修饰符也适用于方法的返回值。指向指针的指针参数也存在类似问题。这些问题以及其他特殊情况，在 *《Objective-C 2.0 编程语言》*¹ 的“远程消息传递”部分中有详细讨论。

### 是对象还是代理？

`NSObject`协议定义了一个特殊方法`-isProxy`，用于确定某个对象是否实际上是`NSProxy`对象。如果该对象引用不是`NSObject`或其子类的实例，则此方法将返回`YES`。使用`-class`、`-className`、`-isMemberOfClass:`或`-isKindOfClass:`等方法无法判断对象是否为代理；代理只会将这些消息转发给原始对象，而原始对象会返回显而易见的答案。例如，使用`-isProxy`来判断直接访问对象的成员变量是否安全，如`if (![character isProxy]) character->hitCount++`。

### 网络

有许多用于通过网络通信的类。其中值得提及的两个是：网络服务，它有助于在网络中注册和定位服务；以及 URL 加载系统，它实现了诸如 HTTP 和 FTP 等各种网络协议。

#### 网络服务

Java 中没有与网络服务完全对应的机制。它的工作方式有点像`java.rmi.Named`，即在局域网内发布服务，并使远程客户端能够轻松浏览和连接这些服务。然而，网络服务要通用得多。它构建在 Bonjour（也称为 Zeroconf）之上。前面演示过的`NSSocketPortNameServer`类，就是使用网络服务在网络中注册和查找分布式对象服务的。

网络服务主要有两个类。一个`NSNetService`对象代表一个单一的服务。

---
¹ Apple, Inc., *《Objective-C 2.0 编程语言》*, http://developer.apple.com/documentation/Cocoa/Conceptual/ObjectiveC/Articles/ocRemoteMessaging.html, 2009 年。



与 `NSConnection` 类似，服务器和客户端进程各自创建一个 `NSNetService` 对象，用于发布服务或连接现有服务。`NSNetServiceBrowser` 则扮演着“媒人”的角色。`NSNetServiceBrowser` 对象会帮助你的应用程序发现当前可用的服务。它可以描述所有可用的资源，搜索特定服务，并与它们建立连接。

使用网络服务需要经历三个操作阶段：

1.  发布
2.  发现
3.  解析

发布是指注册你的服务，使其能够被网络上的其他进程和计算机公开可见。你必须首先创建一个套接字（例如 `NSSocketPort`），客户端可以通过它与你的服务通信。然后，你创建一个 `NSNetService` 对象，连接到该端口，并描述你的服务。接着，通过向 `NSNetService` 对象发送 `-publish` 或 `-publishWithOptions:` 消息来发布服务。发布后，远程客户端就可以找到、连接并与你的服务通信。

使用 `NSNetServiceBrowser` 类来查找你网络上的服务。首先，创建一个 `NSNetServiceBrowser` 实例，然后配置它以查找你感兴趣的服务类型。浏览器会开始工作，并在发现服务时进行报告。

一旦通过浏览或其他方式了解到某个远程服务，最后一步就是解析它。同样，你需要创建一个 `NSNetService` 对象，并用定位该远程服务所需的信息来配置它。然后，向其发送一条 `-resolveWithTimeout:` 消息。随后，`NSNetService` 对象会着手建立连接。成功后，`-getInputStream:outputStream:` 消息会返回输入和输出流对象，通过这些对象，你可以与远程服务进行通信。

网络服务的发布和发现可能非常耗时，并且新的服务可能会自发地出现和消失。因此，几乎所有网络服务的方法都是异步执行的。结果通过你提供的委托对象进行传递。例如，`-resolveWithTimeout:` 消息会立即返回，但会在后台启动解析过程。完成后，你的委托对象会收到 `-netServiceDidResolveAddress:` 消息；如果解析失败，则会收到 `-netService:didNotResolve:` 消息。

[www.it-ebooks.info](http://www.it-ebooks.info/)

《`NSNetServices Programming Guide`》详细介绍了网络服务的工作原理，并提供了大量的代码示例。

#### URL 加载

Java 和 Objective-C 都提供了一组类，用于协助访问由统一资源定位符（URL）标识的服务和实体。两者都内置了对 HTTP、HTTPS、FTP 和 FILE 协议的支持。URL 协议通常很复杂，但这些类巧妙地封装了这些复杂性，使你能够轻松地与它们交互。这使得只需几行代码，即可向 URL（例如 `http://www.apress.com`）发送 Web 服务器请求并接收响应。

Java 和 Objective-C 中 URL 类的组织和角色几乎相同，尽管 Objective-C 的粒度更细一些。`NSURL`（`java.net.URL`）类定义了一个单一的 URL。`NSURLConnection`（`java.net.URLConnection`）管理与 URL 所描述的远程服务的通信。Objective-C 将 Java 单一的 `URLConnection` 对象分解为三个对象：`NSURLConnection` 只关心连接的状态；请求参数封装在 `NSURLRequest` 中；服务的回复包含在 `NSURLResponse` 中。任何数据——请求或响应的“主体”——都通过流或事件进行交换。

本节将介绍几种常见的 URL 加载技术，以及它们与类似 Java 实现的对比。

##### 简单 URL 请求

最简单的 URL 交互就是获取 URL 的内容。两种语言都能轻松实现这一点，如列表 13-10 所示。

**列表 13-10. 简单 URL 请求**

**Java**

```
try {
    URL url = new URL("http://www.apress.com/");
    Reader inStream = new InputStreamReader(url.openStream());
    int c;
    System.out.print("URL Response: ");
    while ((c=inStream.read()) != -1)
        System.out.print((char)c);
    inStream.close();
} catch (Exception e) {
    e.printStackTrace();
}
```

**Objective-C**

```
NSURL *url = [NSURL URLWithString:@"http://www.apress.com/"];
NSURLRequest* request = [NSURLRequest requestWithURL:url];
NSURLResponse *response = nil;
NSData *data = [NSURLConnection sendSynchronousRequest:request returningResponse:&response
                                                  error:NULL];
NSLog(@"URL response: %@",[NSString stringWithCString:[data bytes]
                                              length:[data length]]);
```

Java 代码利用了便捷方法 `java.net.URL.openStream()`，该方法会创建一个 `URLConnection` 对象，发起请求，并返回一个连接到响应流的 `InputStreamReader` 对象。Objective-C 代码的设置稍微复杂一些，首先需要创建一个 `NSURLRequest` 对象。完成这一步后，整个事务通过使用 `+[NSURLConnection sendSynchronousRequest:returningResponse:error:]` 类方法执行。会创建一个临时的 `NSURLConnection` 对象，发送请求，并将响应通过两个对象返回：一个包含回复头部的 `NSURLResponse` 对象和一个包含响应主体的 `NSData` 对象。

##### 异步 URL 请求

Java 中的 URL 交互本质上是同步的。如果加载的 URL 数据需要很长时间才能获取到，列表 13-10 中的代码将会阻塞，直到数据接收完毕。如果加载 URL 所花费的时间无关紧要，这可能是合适的。但通常情况并非如此。为了避免挂起主应用程序，可以将列表 13-10 中的代码放在其自己的线程中执行。在 Java 中，这是首选的解决方案。

在 Objective-C 中，使用 `NSURLConnection` 的自然方式是异步的。大多数 `NSURLConnection` 方法都会启动异步操作——同步操作反而是特例。就像本章前面描述的 `NSStream` 一样，`NSURLConnection` 通过向其委托发送消息来报告其进度。异步读取 URL 内容的代码如列表 13-11 所示。

**列表 13-11. 异步 URL 请求**

```
@interface URLGetter : NSObject {
    NSMutableData *body;
    NSURLConnection *connection;
}
- (void)loadURL:(NSString*)string;
@end

@implementation URLGetter
- (void)loadURL:(NSString*)string
{
    NSURL *url = [NSURL URLWithString:string];
    NSURLRequest *request = [NSURLRequest requestWithURL:url];
    // 分配缓冲区来存储接收到的数据
    body = [NSMutableData data];
    connection = [NSURLConnection connectionWithRequest:request delegate:self];
}

- (void)connection:(NSURLConnection*)connection didReceiveData:(NSData*)data
{
    [body appendData:data];
}

- (void)connection:(NSURLConnection*)connection
didReceiveResponse:(NSURLResponse*)response
{
    // 成功接收到响应头部和元数据
    // 每个新主体之前都会有连续的响应，因此重置缓冲区
    [body setLength:0];
}

- (void)connectionDidFinishLoading:(NSURLConnection*)connection
{
    // URL 加载完成；NSURLConnection 已关闭，|body| 已完成
}

- (void)connection:(NSURLConnection*)connection didFailWithError:(NSError*)error
{
    // URL 加载失败；原因在 |error| 中
}
@end
```



