# 扩展 Content Provider

我们已经看到，内容提供者允许访问类似数据库的内容和文件。如果你对这种实现方式不太满意，或者对内容提供者的功能有自己独特的设计思路，你可以实现 `call()` 方法，如下所示：

```
override fun call(method: String, arg: String, extras: Bundle): Bundle {
    super.call(method, arg, extras)
    // do your own stuff...
}
```

通过这种方式，你可以设计自己的内容访问框架。当然，你应该告知可能的客户端如何使用这个接口，例如在契约类中说明。

### 注意

调用此方法时不进行任何安全检查。你必须自行实现适当的安全检查，例如在上下文中使用 `checkSelfPermission()`。

