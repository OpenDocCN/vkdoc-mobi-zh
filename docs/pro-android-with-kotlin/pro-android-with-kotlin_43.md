# 从 Activity 管理 Fragment

从 Activity 内部管理 Fragment 包括：

- 添加 Fragment，如先前“创建 Fragment”部分所示
- 根据 ID 或标签获取 Fragment 的引用
- 处理返回栈
- 为生命周期事件注册监听器

对于所有这些需求，你可以使用 `getSupportFragmentManager()`，它会给你一个能够处理所有这些事务的 Fragment 管理器，或者在 Kotlin 中直接使用访问器：

```
supportFragmentManager
```

来获取一个引用。

> **注意**
> 还有一个名称中不带“support”的 `fragmentManager`。它指向框架的 Fragment 管理器，而非支持库的 Fragment 管理器。不过，使用支持 Fragment 管理器可以提高与旧版 API 级别的兼容性。

