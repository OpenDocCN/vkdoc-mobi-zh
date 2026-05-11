# 索引



### ![images](img/isquare.jpg) A

ARC 实现，67

`__autoreleasing` 所有权修饰符，77–78

`__strong` 所有权修饰符（*参见* `__strong` 所有权修饰符）

`__unsafe_unretained` 所有权修饰符，78

`__weak` 所有权修饰符（*参见* `__weak` 所有权修饰符）

引用计数，78–80

自动引用计数（ARC），1

`alloc` 方法，13–15

Apple 的实现

`CF/CFRuntime.c` `__CFDoExternRefOperation`，19

GNUstep 实现，20

在 `alloc` 内部，18

Xcode 调试器，18

数组，64–66

自动释放

Apple 的实现，27–28

自动变量，21–24

实现，24–26

`@autoreleasepool` 块，55

`clang`，32

`dealloc` 方法，17–18，54

`id` 和 `void`，56

`__bridge` 转换，56

`__bridge_retained` 转换，57–58

`__bridge_transfer` 转换，58–59

`CFBridgingRelease` 函数，61–63

`CFBridgingRetain` 函数，59–61

实现（*参见* ARC 实现）

命名规则，53

`NSAllocateObject`/`NSDeallocateObject`，52

对象类型变量，55

所有权修饰符（*参见* 所有权修饰符）

属性，63–64

引用计数内存管理，1

引用计数机制，32

参考文献，187

`release` 方法，17

`retain` 方法，15

`retain`、`release`、`retainCount` 和 `autorelease`，52

源文件，31

在 Twitter/Tumblr 客户端中，181–185

区域（`NSZone`），55

`__autoreleasing` 所有权修饰符，77–78

`@autoreleasepool` 和变量，46

编译器，46–48

`NSError` 指针，48–50

`__strong` 和 `__weak` 变量，51

### ![images](img/isquare.jpg) B、C、D、E、F

块，93

自动变量捕获

`__block` 说明符，90

C 数组，91–92

编译错误，90

`fmt` 和 `val`，89

`NSMutableArray` 类，91

自动变量

匿名函数，102–103

块字面量，102

源代码，101–102

块字面量语法

BNF 范式，86

脱字符函数，86

C 语言中的函数声明，85

返回类型，86

无返回类型，86–87

块类型变量

`blk` 变量，88

声明，87

`funcptr` 变量，88

函数指针类型，87

Objective-C 方法，89

`__block` 变量和对象，128–131

捕获对象，123–128

C++ 实例方法，95–96

循环引用，131–136

拷贝/释放函数，136–138

`_cself` 声明，96–97

`__main_block_impl_0` 实例，97–98

`_main_block_impl_0` 结构体，97

内存段

`__block` 变量，118–123

拷贝函数，114–118

堆，113–114

`_NSConcreteGlobalBlock` 类，110–113

`_NSConcreteMallocBlock` 类，111

`_NSConcreteStackBlock` 类，110

`_NSConcreteStackBlock`，99–100

入门

匿名函数，82

其他语言名称，85

变量，83–84

参考文献，188

`self`，Objective-C 实例方法，95

源代码转换，93–95

在 Twitter/Tumblr 客户端中，181–85

可写变量，104

`__block` 说明符，106–110

静态/全局变量，104–106



## G, H, I, J, K, L

Grand Central Dispatch (GCD)，139，147，173

Cocoa 框架，140

定义，139

`dispatch` I/O，171–172

`dispatch` 队列，147，173

块执行，176

并发调度队列，148–150

`dispatch_after`，157–158

`dispatch_apply`，165–167

`dispatch_barrier_async`，161–163

调度组，158–161

`dispatch_once`，170

`dispatch_queue_create`，150–153

调度信号量，167–170

`dispatch_set_target_queue`，156–157

`dispatch_suspend/resume`，167

`dispatch_sync`，163–165

全局调度队列，175–176

内核级实现，173–174

主/全局调度队列，153–155

`pthread_workqueue`，175–176

串行调度队列，148

软件组件，174

任务执行，147

调度源，177

文件描述符，178–179

定时器，179–180

类型，177

多线程编程，141

优势，145

CPU 执行，143–144

劣势，144

`performSelector` 方法，140

参考，190

在 Twitter/Tumblr 客户端中，181–185

## M, N

内存区段，块

`__block` 变量

`__forwarding`，121–123

多个块，120

从栈到堆，118

复制函数

自动复制，114–115

手动复制，115–117

多次复制，117–118

堆，113

`_NSConcreteGlobalBlock` 类，110–113

`_NSConcreteMallocBlock` 类，111

`_NSConcreteStackBlock` 类，110

多线程编程

优势，145

CPU 执行，143–144

死锁，144

劣势，144

在 Mac/iPhone 上，142

主线程，144

Objective-C 源代码，141

竞态条件，144

## O, P, Q

所有权修饰符

`__autoreleasing` 所有权修饰符

`@autoreleasepool` 与变量，46

编译器，46–48

`NSError` 指针，48–50

`__strong` 和 `__weak` 变量，51

`__strong` 所有权修饰符

成员变量与方法参数，36–37

非 ARC 环境，33–34

`NSMutableArray`，34

所有权状态，34

引用计数规则，38

`release` 方法，34

源代码，37

强引用工作机制，35–36

`__unsafe_unretained` 所有权修饰符

编译器警告消息，43

变量，45

对比 `__weak` 所有权修饰符，44

带注释，44–45

`__weak` 所有权修饰符

循环引用，38–40

自身引用，40–42

弱引用消失，42–43

`id` 与对象类型变量，33

## R

引用计数

办公室灯光的类比，1

计数器管理，3

对比 Objective-C 对象，3

问题，2

Objective-C 对象与方法，5

所有权

对象创建，6–7

使用 retain 持有对象，15–16

释放所有权，8–13

规则，5

## S, T

`__strong` 所有权修饰符，67

数组方法，68

成员变量与方法参数，36–37

非 ARC 环境，33–34

`NSMutableArray`，34

`objc_msgSend`，68

`objc_retainAutoreleasedReturnValue` 函数，68，69

所有权状态，34

引用计数规则，38

`release` 方法，34

跳过注册，70

源代码，37

强引用工作机制，35–36

## U, V

`__unsafe_unretained` 所有权修饰符

编译器警告消息，43

变量，45

对比 `__weak` 所有权修饰符，44

带注释，44–45

## W, X, Y, Z

`__weak` 所有权修饰符

`allowsWeakReference` 方法，76

自动释放池，对象添加，74

循环引用，38–40

特性，70

对象的即时销毁，73

`objc_clear_deallocating` 函数，72

`objc_initWeak` 函数，71

`objc_loadWeakRetained` 函数，74

`objc_release` 函数，71

`objc_storeWeak` 函数，71

`retainWeakReference` 方法，76

自身引用，40–42

弱引用消失，42–44
