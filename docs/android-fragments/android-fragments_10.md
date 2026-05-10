# 参考文档

- `fragments.html`：Android 开发者指南中关于 Fragments 的页面：[`developer.android.com/guide/components/fragments.html`](http://developer.android.com/guide/components/fragments.html)
- `multi-pane-layouts.html`：Android 关于多面板布局的设计指南：[`developer.android.com/design/patterns/multi-pane-layouts.html`](http://developer.android.com/design/patterns/multi-pane-layouts.html)
- `fragments/index.html`：Android 关于 Fragments 的培训页面：[`developer.android.com/training/basics/fragments/index.html`](http://developer.android.com/training/basics/fragments/index.html)

## 总结

本章介绍了`Fragment`类及其相关的管理器、事务和子类。以下是本章涵盖内容的摘要：

- `Fragment`类及其功能和用法。
- 为什么 Fragment 必须依附于一个且仅一个 Activity 才能使用。
- 虽然 Fragment 可以通过静态工厂方法（例如`newInstance()`）实例化，但必须始终提供一个默认构造函数，以及一种将初始化值保存到初始化参数 Bundle 中的方式。
- Fragment 的生命周期，以及它如何与拥有该 Fragment 的 Activity 的生命周期相互交织。
- `FragmentManager`及其特性。
- 使用 Fragment 管理设备配置。
- 将多个 Fragment 合并到一个 Activity 中，或将它们拆分到多个 Activity 中。
- 使用 Fragment 事务更改用户看到的内容，并通过酷炫的效果为这些过渡添加动画。
- 使用 Fragment 时，返回按钮可实现的新行为。
- 在布局中使用`<fragment>`标签。
- 当需要使用过渡时，使用`FrameLayout`作为 Fragment 的占位符。
- `ListFragment`以及如何使用适配器填充数据（与`ListView`非常相似）。
- 当 Fragment 无法适应当前屏幕时启动新 Activity，以及当配置变更使得可以再次看到多个 Fragment 时如何进行适配。
- Fragment 之间以及 Fragment 与其 Activity 之间的通信。

---

