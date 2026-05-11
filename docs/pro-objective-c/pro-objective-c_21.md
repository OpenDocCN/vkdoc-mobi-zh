# 排版后内容

要`vend`一个对象（即，使一个对象实例分布式化，以便其他应用可以通过网络调用它），应首先将该对象配置为`NSConnection`实例的根对象，然后将该连接注册到网络上。Listing 12-18 导出了一个名为`helloServiceObj`的对象，并将其注册在名称`HelloService`下。

*Listing 12-18.* 注册 HelloService 分布式对象

```
NSConnection *connection = [NSConnection defaultConnection];
[connection setRootObject:helloServiceObj];
[connection registerName:@"HelloService"];
```

注册使得分布式对象对远程客户端可用。连接必须在运行循环（即一个`NSRunLoop`实例）内启动，以捕获对分布式对象的传入请求。

客户端可以通过获取分布式对象的代理来调用其上的方法。以下语句获取了 Listing 12-18 中所示分布式对象在本地主机上的代理。

```
NSDistantObject *helloProxy =
  [NSConnection rootProxyForConnectionWithRegisteredName:@"HelloService"
                                                    host:nil];
```

`NSDistantObject`的`setProtocolForProxy:`方法用于设置由分布式对象处理的方法。如果 Listing 12-18 中的分布式对象采用了名为`HelloProtocol`的协议，并且其方法包含了客户端可用的方法，那么可以使用以下语句来配置名为`helloProxy`的代理以支持该协议。

```
[helloProxy setProtocolForProxy: @protocol (HelloProtocol)];
```

`NSInvocation`和`NSMethodSignature`类用于支持 Foundation 框架的分布式对象系统。`NSInvocation`类在第 9 章中介绍过，其中你了解到`NSInvocation`对象由 Objective-C 消息的所有元素组成：接收（目标）对象、选择器、参数和返回值。`NSMethodSignature`用于转发接收（分布式）对象无法响应的消息。它包含方法参数和返回值的类型信息。

分布式对象类仅在 Mac OS X 平台上可用。

## 脚本化

Foundation 框架的脚本化类支持创建*可脚本化应用程序*，即可以通过 AppleScript（一种编程语言，能够直接控制可脚本化应用程序以及 Apple OS X 中可脚本化的部分）进行控制的应用程序。

`NSScriptCommand`及其子类实现了标准的 AppleScript 命令。`NSScriptObjectSpecifier`及其子类定位可脚本化对象。`NSScriptCoercionHandler`和`NSScriptKeyValueCoding`执行与脚本化相关的基本功能。

脚本化类仅在 OS X 平台上可用。

## 总结

在本章中，你继续回顾了 Foundation 框架，重点介绍了为 Objective-C 程序提供特定系统服务的类。你现在应该熟悉提供以下功能的 Foundation 框架类：

*   通知
*   归档与序列化
*   分布式对象
*   脚本化

---

