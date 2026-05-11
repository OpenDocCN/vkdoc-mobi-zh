# 问题 55：instancetype 是如何工作的，它有什么用处？

`instancetype` 是一个上下文相关的关键字，可以用作返回类型，表示一个方法返回一个相关的结果类型。例如：

```
@interface Person
+ (instancetype)personWithName:(NSString *)name;
@end
```

为了评估候选人的学习曲线，一个后续问题可能是讨论使用 `instancetype` 和 `id` 之间的异同。与 `id` 不同，`instancetype` 只能在方法声明中用作结果类型。

