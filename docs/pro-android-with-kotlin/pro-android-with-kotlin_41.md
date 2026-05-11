# 上下文操作模式

*上下文操作模式* 的行为类似于与上下文相关的应用栏。标准应用栏对应用来说是静态的，而上下文操作模式则是视图特定的。对于这种上下文菜单，你需要执行以下操作：

- 实现 `ActionMode.Callback` 接口。
- 在 `onCreateActionMode(...)` 中创建菜单，类似于之前标准上下文菜单的 `onCreateContextMenu()`。
- 在 `onPrepareActionMode(...)` 中返回 `false`，除非你需要特殊的准备步骤。
- 实现 `onActionItemClicked(...)` 来监听触摸事件。
- 像创建标准上下文菜单那样，创建一个菜单 XML 资源文件。
- 在代码中，通过调用 `startActionMode( theActionModeCallback )` 打开上下文操作模式。

## 弹出菜单

上下文相关菜单主要用于不常更改的设置，而属于前端工作流程的菜单最好以弹出菜单的形式实现。弹出菜单通常由用户与特定视图交互而触发，因此弹出菜单会分配给 UI 元素。

在用户执行某些操作后显示视图的弹出菜单，可以简单地通过调用一个函数来实现：

```kotlin
fun showPopup(v: View) {
PopupMenu(this, v).run {
setOnMenuItemClickListener { menuItem ->
Toast.makeText(this@TheActivity,
menuItem.toString(),
Toast.LENGTH_LONG).show()
true
}
menuInflater.inflate(popup, menu)
show()
}
}
```

像往常一样，你还需要在 `res/menu` 资源中定义菜单——例如，一个名为 `popup.xml` 的文件（正如在 `inflate(...)` 的第一个参数中看到的那样）。

## 进度条

显示进度条是改善需要运行几秒钟的任务用户体验的好方法。要实现进度条，请向布局中添加一个 `ProgressBar` 视图。无论是在 XML 布局文件中还是在 Kotlin 程序中执行此操作，都没有关系。在 XML 中，你编写：

```xml
<ProgressBar
    android:id="@+id/progressBar"
    style="?android:attr/progressBarStyleHorizontal"
    android:layout_width="match_parent"
    android:layout_height="wrap_content" />
```

用于不确定进度条（如果你无法以百分比值表示进度），或者：

```xml
<ProgressBar
    android:id="@+id/progressBar"
    style="?android:attr/progressBarStyleHorizontal"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:max="100" />
```

用于确定进度条（在更新进度条时你知道百分比）。

在代码中，你可以通过以下方式切换不确定进度条的可见性：

```kotlin
progressBar.visibility = View.INVISIBLE
// 或 .. = View.VISIBLE
```

要设置确定进度条的进度值，请参阅以下示例：

```kotlin
with(progressBar) {
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O)
min = 0
max = 100
}
var value = 0
Thread {
while(value < 100) {
value += 1
Thread.sleep(200)
runOnUiThread {
progressBar.progress = value
}
}
progressBar.visibility = View.INVISIBLE
}.start()
```

这里我们使用一个后台线程来模拟耗时较长的任务——在真实应用中，后台会执行更有意义的事情。也许你会使用 `AsyncTask` 而不是线程。

## 使用 Fragment

*Fragment* 是 Activity 的一个可重用的模块化部分。如果你开发一个没有使用 Fragment 的非平凡应用，你会有一系列相互调用的 Activity。这样带来的问题是，在屏幕较大的设备上，如果 Activity 之间保持紧密关联，这可能不是最佳的解决方案。例如，一个 Activity 中显示某些项目的列表，而另一个 Activity 中显示选定项目的详细信息。在小屏幕设备上，当用户点击列表 Activity 中的项目时启动详细信息 Activity 是完全可以接受的。然而在较大的屏幕上，如果列表和详细信息两个视图能够并排显示，可能会改善用户体验。

这就是 Fragment 派上用场的地方。你不是在 Activity 之间切换，而是创建多个属于*同一* Activity 的 Fragment。然后，根据设备的不同，你可以选择单窗格视图或双窗格视图。

如果你从一个仅包含 Activity 的应用进行迁移，那么开发 Fragment 很简单。Fragment 具有与 Activity 相同的生命周期，并且生命周期回调与 Activity 的非常相似（如果不是完全相同的话）。不过，这里的情况会变得稍微复杂一些，因为容器 Activity 的生命周期和所包含 Fragment 的生命周期彼此关联，并且 Fragment 还表现出专门的调用栈行为。Fragment 的在线文档为你提供了所有与 Fragment 相关问题的详细参考。但在本节中，我们将仅介绍创建和使用 Fragment 的基本方面。

### 创建 Fragment

创建 Fragment 有两种选择：要么在布局文件中指定 `Fragment` 元素，要么从 Kotlin 代码中以编程方式添加 Fragment。

要使用 XML 方式将 Fragment 添加到你的应用中，在布局文件中，确定合适的位置来添加 Fragment，然后编写：

```xml
<fragment
    android:name="com.example.myapp.MyFragment"
    android:id="@+id/myFragment"
    android:layout_width="match_parent"
    android:layout_height="match_parent" />
```

其中布局参数需要根据应用的布局需求进行选择。对于不同的设备（更准确地说是不同的屏幕尺寸），你可以提供几个不同的布局文件，这些文件在不同位置包含不同数量的 Fragment。

对于由 XML 文件中的 "name" 属性指定的 Fragment 类，从一个最简的类开始，比如：

```kotlin
import android.support.v4.app.Fragment
...
class MyFragment : Fragment() {
override
fun onCreateView(inflater: LayoutInflater?,
container: ViewGroup?,
savedInstanceState: Bundle?): View? {
return inflater!!.inflate(
my_fragment, container, false)
}
}
```

并将一个名为 `my_fragment.xml` 的布局文件添加到 `res/layout` 资源文件夹中。



