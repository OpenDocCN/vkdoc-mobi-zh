# 上下文

Android 中的另一个核心组件是 `Context`。`Context`^(⁸) 在 Android 中常被称为“上帝”类，是一个用于检索应用程序环境全局信息的接口。它允许访问特定于应用程序的资源以及应用程序级别的操作，例如广播、接收 Intent 和启动 Activity。一些用例包括：

*   访问应用程序内部存储的位置
*   发送 Toast 或通知对话框
*   在 Activity 中设置 `ImageView`
*   检索系统包管理器

`Context` 有两种主要类型：应用程序上下文（application context）和 Activity 上下文（activity context）。这两种类型的 `Context` 都绑定到各自领域的生命周期——应用程序上下文与应用程序的生命周期绑定，而 Activity 上下文则与其 Activity 的生命周期绑定。这意味着如果其中任何一个被销毁，那么它们各自的上下文将被垃圾回收。

除了 `Context` 的这两个子类之外，还有 `ContextWrapper`，它可以与 `Context` 方法 `getBaseContext()` 一起使用。`ContextWrapper` 允许使用代理 `Context`，这样可以在不更改原始 `Context` 的情况下修改对象的行为。

## 应用程序上下文

以下方法返回应用程序上下文。当应用程序被销毁时，它将被垃圾回收。

*检索应用程序上下文：*

```
getApplicationContext()
```

## Activity 上下文

当在 `Activity` 或 `Activity` 的子类中时，使用 `this` 返回 Activity 上下文。当 Activity 被销毁时，Activity 上下文将被垃圾回收。

*从 Activity 内部检索 Activity 上下文：*

```
this
```

