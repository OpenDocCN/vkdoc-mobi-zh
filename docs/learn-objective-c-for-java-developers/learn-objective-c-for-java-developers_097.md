# 使用垃圾回收的内存管理

即使在 Objective-C 中使用垃圾回收，Core Foundation 依然采用引用计数。垃圾收集器能够感知 Core Foundation 的引用计数，并将其整合到收集逻辑中。具体来说，只有当某个 Core Foundation 类型没有 **__strong** 引用**并且**其 Core Foundation 引用计数为 0 时，它才会被销毁（回收）。这使得 Core Foundation 和 Objective-C 垃圾回收能够共存，但在将 `CFTypeRef` 当作 Objective-C 对象处理时仍需注意。与 Objective-C 不同，使用垃圾回收时不能简单地忽略引用计数。

当将 Core Foundation 类型存储在 Objective-C 对象指针（或任何 **__strong** 或 **__weak** 指针类型）中时，需要通过 `CFMakeCollectable(CFTypeRef)` 函数将其过渡到垃圾回收，如清单 25-2 所示。该函数执行一次 `CFRelease()`——将其引用计数减至 0，但不会销毁该类型——并返回一个指向它的 **__strong** 引用。该类型/对象现在由垃圾收集器负责。

2 Apple Inc., *Core Foundation 内存管理编程指南*, [`developer.apple.com/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/`](http://developer.apple.com/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/), 2007.

[www.it-ebooks.info](http://www.it-ebooks.info/)

## 第 25 章 ■ 混合 C 与 Objective-C

**注意** 垃圾回收只能与使用标准或默认分配器函数（`kCFAllocatorDefault` 或 `NULL`）分配的 Core Foundation 类型一起使用。幸运的是，对于您处理的大多数 Core Foundation 类型来说，这都适用。使用其他分配器（特别是 `kCFAllocatorMalloc`）分配的 Core Foundation 类型将在另一个内存区域中分配，并且**不会**被垃圾收集器扫描。任何此类类型都必须使用 Core Foundation 内存管理进行管理。

尽管 `CFRetain()` 和 `CFRelease()` 在功能上与 `-[NSObject retain]` 和 `-[NSObject release]` 相同，但在垃圾回收环境中，您不能使用 Objective-C 消息来保留和释放 Core Foundation 类型。请记住，当垃圾收集器启动时，Objective-C 消息 `-retain` 和 `-release` 会被禁用。因此，虽然 `CFRetain()` 和 `CFRelease()` 继续有效，但 `-retain` 和 `-release` 不做任何操作。

#### 在托管内存中使用 Core Foundation

在托管内存环境中，Core Foundation 和 Objective-C 共享相同的引用计数机制。在这种环境中，`CFRetain()` 和 `CFRelease()` 等同于 `-[NSObject retain]` 和 `-[NSObject release]`。

处理已保留的 Core Foundation 类型的最简单方法是立即将其添加到自动释放池中。如果清单 25-2 中的 `-uuid` 方法是为托管内存环境编写的，它将类似于清单 25-3 中的代码。

***清单 25-3.** 返回自动释放的 Core Foundation 引用*

```
- (NSString*)uuid
{
    CFStringRef cfString = CFUUIDCreateString(kCFAllocatorDefault, uuid);
    return [(NSString*)cfString autorelease];
}
```

清单 25-3 中由 `-uuid` 返回的对象是自动释放的，其行为与任何其他自动释放的字符串对象相同。第 24 章中描述的大多数托管内存模式同样适用于 Core Foundation 类型。

### 总结

从 Objective-C 调用 C 函数非常简单，让您的代码可以直接访问基于 C 的解决方案。免费桥接使得处理 Core Foundation 类型变得更加容易，允许您像处理 Objective-C 对象一样与许多 Core Foundation 类型交互。最常见的问题是匹配或调整两种语言的内存管理风格。

[www.it-ebooks.info](http://www.it-ebooks.info/)

---

