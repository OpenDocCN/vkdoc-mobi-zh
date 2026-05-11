# 从 Kotlin 代码中添加 Fragment

要从 Kotlin 代码内部添加 Fragment，你需要确定一个用于放置 Fragment 的布局容器或 `ViewGroup`，然后使用 Fragment 管理器执行插入操作：

```
with(supportFragmentManager.beginTransaction()) {
    val fragment = MyFragment()
    add(fragm_container.id, fragment, "fragmTag")
    val fragmentId = fragment.id // 之后可以使用...
    commit()
}
```

例如，这可以发生在 Activity 的 `onCreate()` 回调中，或者更动态地在其他任何合适的位置。`fragm_container` 例如是布局 XML 中的一个 ` <FrameLayout>` 元素。

> **注意**
> Fragment 在事务中添加时，会从 Fragment 管理器获取其 ID。在此之前你无法使用它，并且如果在 Kotlin 代码中添加 Fragment，你也无法提供自己的 ID。

