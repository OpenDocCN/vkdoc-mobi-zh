# 你可以使用 `retain` 获取对象的所有权

有时，不属于 `alloc`/`new`/`copy`/`mutableCopy` 方法组的方法会返回一个对象。在这种情况下，你并未创建它，因此你对其没有所有权。以下是使用 `NSMutableArray` 类方法 `array` 的示例。

```
/*
 * 获取一个对象，而不自行创建或拥有所有权
 */

id obj = [NSMutableArray array];

/*
 * 获取的对象存在，但你对其没有所有权。
 */
```

变量 `obj` 持有对 `NSMutableArray` 对象的引用，但你并不拥有其所有权。要获取所有权，你必须使用 `retain` 方法。

```
/*
 * 获取一个对象，而不自行创建或拥有所有权
 */

id obj = [NSMutableArray array];

/*
 * 获取的对象存在，但你对其没有所有权。
 */

[obj retain];

/*
 * 现在你拥有了该对象的所有权。
 */

[obj release];

/*
 * 对象的所有权已被释放。
 *
 * 虽然变量 obj 还持有指向该对象的指针，
 * 但你不能继续访问该对象了。
 */
```

调用 `retain` 方法后，你就拥有了该对象的所有权，就好像你是通过 `alloc`/`new`/`copy`/`mutableCopy` 方法组获得它一样。

