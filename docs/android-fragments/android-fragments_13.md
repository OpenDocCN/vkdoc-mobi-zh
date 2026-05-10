# 优化排版后的内容

让 Android 提供合适的资源。

使用单例在 Activity 外部保存数据，以便在配置变更时更轻松地销毁和重建 Activity。

利用默认的 `onSaveInstanceState()` 回调来保存带有 `android:id` 的视图上的 UI 状态。

如果某个 Fragment 在 Activity 的销毁与重建周期中能够无问题地存活，可以使用 `setRetainInstance()` 来告知 Android 无需销毁并重建该 Fragment。

---

