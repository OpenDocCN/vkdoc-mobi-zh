# 客户端通过 Content Provider 接收变更通知

一个客户端通过其 `ContentResolver` 字段（例如 `Activity.contentResolver`）访问内容提供者时，可以注册以接收内容变更的通知，调用方式如下：

```
val uri = ... // a content uri
contentResolver.registerContentObserver(uri, true,
    object : ContentObserver(null) {
        override fun onChange(selfChange: Boolean) {
            // do something
        }
        override fun onChange(selfChange: Boolean,
                              uri: Uri?) {
            // do something
        }
    }
)
```

`registerContentObserver()` 的第二个参数指定子 URI（即原 URI 加上更多路径元素）是否也会触发通知。`ContentObserver` 的构造函数参数也可以是一个 `Handler` 对象，用于在不同线程中接收 `onChange` 消息。

为了使这一机制正常工作，在内容提供者端，你可能需要确保事件被正确触发。例如，在任何数据修改方法内部，你应该添加：

```
context.contentResolver.notifyChange(uri, null)
```

此外，为了使变更监听更加健壮，你可能希望通知由 `query()` 方法返回的所有 `Cursor` 对象。为此，`cursor` 提供了 `registerContentObserver()` 方法，你可以用它来收集基于游标的内容观察者。内容提供者随后也会向这些内容观察者发送消息。

