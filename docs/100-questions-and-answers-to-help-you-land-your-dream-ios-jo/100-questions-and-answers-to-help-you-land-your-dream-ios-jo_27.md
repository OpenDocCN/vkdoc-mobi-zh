# 问题 93：什么是惰性存储属性？它们在什么时候有用？

惰性存储属性用于那些初始值直到第一次使用时才被计算的属性。你可以在属性声明前写上 `lazy` 修饰符来声明一个惰性存储属性。当一个属性的初始值依赖于外部因素，而这些因素的值未知时，惰性属性非常有用。



## 问题 94：你听说过 Handoff 吗？

Handoff 是随着 iOS 8 和 OS X Yosemite 引入的一项新功能。它允许你在从一台设备切换到另一台设备时，无需重新配置任何设备即可不间断地继续某项活动。

## 问题 95：在 iOS 中可以有多个 UIWindow 吗？

虽然这是可能的，但情况比较少见。例如，当你在一个独立的窗口中有一个 `UIAlertView` 时，就会发生这种情况。

## 问题 96：什么是 Metal？

Metal 是一个底层、可移植性较差、但优化程度更高的图形层，用于执行与 OpenGL（及 OpenCL）或 Direct3D 相同类型的任务，即 3D 图形渲染和并行 GPU 编程。出于性能原因，Metal 试图降低所需的开销量，并减少 CPU 瓶颈。

## 问题 97：你能想出提高网络效率的策略吗？

一名有经验的开发者应该了解一些 iOS 默认未提供的技术。这将提升你的水平。

你可以讨论诸如延迟测量（评估你所连接的网络类型，以便做出数据传输决策）、批处理（将请求打包在一起，当它们成为一个有代表性的组时一起发送——许多分析 SDK 中都会这样做）、预取（在设备空闲或网络连接良好时下载信息）、指数退避（如果连接失败，在发出下一个请求之前等待指数级增加的时间间隔）、缓存机制、使用 Last-Modified 头等主题。

## 问题 98：你在上一份工作中必须解决的最复杂问题是什么？

这个问题将揭示候选人具备的、与 iOS 知识不直接相关但同样重要的技能。这可以引导你讨论应用程序增长、系统扩展的问题，如何使软件长期可持续发展，如何组建团队并使其高效工作，以及候选人用来解决问题的框架。

## 问题 99：什么是自动释放池？

`NSAutoreleasePool` 类用于支持 Cocoa 的引用计数内存管理系统。自动释放池存储对象，当池本身被清空时，会向这些对象发送释放消息。另外，如果你使用自动引用计数，就不能直接使用自动释放池。相反，你应该使用 `@autoreleasepool` 块。

## 问题 100：从 UIButton 到 NSObject 的类层次结构是怎样的？

`UIButton` 继承自 `UIControl`，`UIControl` 继承自 `UIView`，`UIView` 继承自 `UIResponder`，`UIResponder` 继承自根类 `NSObject`：`UIButton` ➤ `UIControl` ➤ `UIView` ➤ `UIResponder` ➤ `NSObject`。

## 我可以请你帮个忙吗？

如果你喜欢这本书，并且/或觉得它信息丰富或有帮助，如果你能在亚马逊上留下简短的评论，我将不胜感激。我会阅读所有评论，以便能继续撰写人们感兴趣的内容。

反馈、更正、问题、疑问、建议？欢迎随时通过电子邮件 eenriquelopez@gmail.com 与我联系。

感谢你的支持！

### 索引

#### A

AFNetworking
alloc 和 init
App ID
苹果文档指出
应用程序 iOS iPhone 和 iPad 问题
应用沙盒
数组迭代
序列化
`NSAssert` 函数
关联值
异步任务处理
原子属性
自动布局
自动引用计数 (ARC)
对象析构
自动释放池

#### B

iOS 后台模式
Block 相关问题
Bug 跟踪
Bundle ID

#### C

代码签名
代码测试
自动化测试
手动测试
可测试的代码
`XCTest`
对象的拷贝与保留
Swift 自定义运算符

#### D, E

深拷贝
析构器
委托与通知
委托
字典

#### F

Fallthrough 语句

#### G

Grand Central Dispatch (GCD)

#### H

Handoff
HashMap

#### I

`#if` 和 `#ifdef`
`Info.plist` 文件
`instancetype` 关键字
iOS
实现并发
App ID 和 Bundle ID
应用程序
应用被拒
异步任务处理
后台模式
分类
编译器
调试与性能分析
依赖管理
设计模式
扩展
frame 与 bounds
框架
Instruments
内存管理
MVC
安全
为 UIView 指定布局
SQL 注入攻击
存储信息
结构体
其中的 `UIWindows`
iPhone 和 iPad 应用程序

#### J

Web 服务器 JSON

#### K

键值编码 (KVC)
键值观察 (KVO)
委托和
KVC 和

#### L

图层对象
延迟存储属性
`let` 关键字

#### M

托管对象上下文功能
内存泄漏
防止内存不足崩溃
指针
内存管理
Metal
多线程
MVC
结合 Reactive Cocoa 的 MVVM

#### N

网络效率
网络
网络安全
nil 指针
非原子行为
`NSArray`
`NSAssert`
`NSAutoreleasePool` 类
`NSCoder`
`NSCoding`
`NSJSONSerialization`
`NSNotification`
`UIButton` 到 `NSObject` 类层次
`NSOperationQueue` 类
`NSSet`
`NSString`
格式化代码
新方法
`NSUserDefaults`

#### O

ARC 下的对象析构
Objective-C
函数
反射
类型转换
可选链
`@optional` 方法

#### P

编程式 `UIView`
优点
缺点
`@property` 变量
协议
配置文件

#### Q

查询过程

#### R

原始值
反射
资源组
响应者链
`-retainCount`

#### S

沙盒过程
合理性检查
浅拷贝
单例
智能指针
SQL 注入
故事板
优点
缺点
强引用
结构体
Swift
ARC
集合类型
自定义运算符
析构器
扩展
`let` 和 `var` 关键字
方法调配
`@synchronized` 关键字
`@synthesize` 变量
合成属性

#### T

首选工具
跟踪过程

#### U

`UIColor` 类
`UIResponder` 对象
`UITableViewController` 的必要考虑
`UITableViewDelegate`
iOS 中的 `UIWindows`
未知类型作为参数

#### V

`var` 关键字
`viewDidAppear()` 方法
`viewDidLoad()` 方法
`viewDidUnload` 的目的
Outlet
IP 语音 (VoIP)

#### W

弱引用

#### X, Y, Z

XIB 文件
优点
缺点
