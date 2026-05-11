# 批量访问内容数据

`android.content.ContentProvider` 类允许你的实现使用：

```
applyBatch(
operations: ArrayList):
Array
```

默认实现会遍历列表并按顺序执行每个操作，但你也可以重写该方法以使用你自己的逻辑。参数中提供的 `ContentProviderOperation` 对象描述了要执行的操作——可以是更新、删除或插入之一。为方便起见，该类提供了一个构建器，你可以像以下示例一样使用它：

```
val oper:ContentProviderOperation =
ContentProviderOperation.newInsert(uri)
.withValue("key1", "val1")
.withValue("key2", 42)
.build()
```

