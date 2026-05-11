# 使用 Kotlin 的高级监听器

无论你正在为 Android 创建哪种应用，总会在某个地方，或者更可能是经常需要为 API 函数调用提供监听器。在 Java 中，你必须创建实现监听器接口的类或匿名内部类，而在 Kotlin 中，你可以更优雅地做到这一点。

如果你有一个 SAM（即*单抽象方法*）类或接口，事情就很简单。例如，如果你想给按钮添加点击监听器，这意味着你需要提供 `View.OnClickListener` 接口的实现，用 Java 方式看起来是这样的：

```
btn.setOnClickListener(object : View.OnClickListener {
override fun onClick(v: View?) {
// 执行某些操作
}
})
```

然而，由于这个接口只有一个方法，你可以更简洁地编写：

```
btn.setOnClickListener {
// 执行某些操作
}
```

并让编译器找出接口方法该如何实现。

如果监听器不是 SAM，即它有多个方法，这种简写方式就不再可行。例如，如果你有一个 `EditText` 视图并想添加文本改变监听器，基本必须这样写：

```
val et = ... // EditText 视图
et.addTextChangedListener( object : TextWatcher {
override fun afterTextChanged(s: Editable?) {
// ...
}
override fun beforeTextChanged(s: CharSequence?,
start: Int, count: Int, after: Int) {
// ...
}
override fun onTextChanged(s: CharSequence?,
start: Int, before: Int, count: Int) {
// ...
}
})
```

即使你只关心 `onTextChanged()` 回调方法也是如此。不过，你可以做的是在工具文件中扩展 `EditText` 类，并在其中添加提供简化文本改变监听器的可能性。为此，首先创建一个这样的文件，例如 `utility.kt`，放在 `com.example` 包内，或者当然也可以是应用的任何包内。在其中添加：

```
fun EditText.addTextChangedListener(l:
(CharSequence?, Int, Int, Int) -> Unit) {
this.addTextChangedListener(object : TextWatcher {
override fun afterTextChanged(s: Editable?) {
}
override fun beforeTextChanged(s: CharSequence?,
start: Int, count: Int, after: Int) {
}
override fun onTextChanged(s: CharSequence?,
start: Int, before: Int, count: Int) {
l(s, start, before, count)
}
})
}
```

这会动态地向该类添加所需的方法。

现在，你可以在任何需要的地方使用 `import com.example.utility.*`，然后这样写：

```
val et = ... // EditText 视图
et.addTextChangedListener({ s: CharSequence?,
start: Int, before: Int, count: Int ->
// 执行某些操作
})
```

与原始结构相比，这看起来简洁得多。

### 多线程

我们已经在第 9 章的“后台任务”部分一定程度上讨论过多线程。本节仅指出 Kotlin 在语言层面可以做些什么来简化多线程。

Kotlin 在其标准库中包含了一些实用函数。与使用 Java API 相比，它们有助于更轻松地启动线程和计时器——参见表 11-1。

**表 11-1**  
Kotlin 并发

| 名称 | 参数 | 返回值 | 描述 |
| --- | --- | --- | --- |
| `fixedRateTimer` | `name: String?` `daemon: Boolean` `initialDelay: Long` `period: Long` `action: TimerTask.() -> Unit` | `Timer` | 创建并启动一个用于固定速率调度的计时器对象。`period` 和 `initialDelay` 参数以毫秒为单位。 |
| `fixedRateTimer` | `name: String?` `daemon: Boolean` `startAt: Date` `period: Long` `action: TimerTask.() -> Unit` | `Timer` | 创建并启动一个用于固定速率调度的计时器对象。`period` 参数以毫秒为单位。 |
| `timer` | `name: String?` `daemon: Boolean` `initialDelay: Long` `period: Long` `action: TimerTask.() -> Unit` | `Timer` | 创建并启动一个用于固定速率调度的计时器对象。`period` 参数是前一个任务结束到下一个任务开始之间的时间（毫秒）。 |
| `timer` | `name: String?` `daemon: Boolean` `startAt: Date` `period: Long` `action: TimerTask.() -> Unit` | `Timer` | 创建并启动一个用于固定速率调度的计时器对象。`period` 参数是前一个任务结束到下一个任务开始之间的时间（毫秒）。 |
| `thread` | `start: Boolean` `isDaemon: Boolean` `contextClassLoader: ClassLoader?` `name: String?` `priority: Int` `block: () -> Unit` | `Thread` | 创建并可能启动一个线程，执行其 `block`。优先级较高的线程将优先于优先级较低的线程执行。 |

对于计时器函数，“action”参数是一个闭包，其中 `this` 是相应的 `TimerTask` 对象。使用它，你可以例如从其执行块内部取消计时器。当所有非守护线程退出后，将 `daemon` 或 `isDaemon` 设置为 `true` 的线程或计时器不会阻止 JVM 关闭。

凭借其通用的函数能力，Kotlin 无论如何都能很好地帮助我们处理并发——`java.util.concurrent` 中许多处理并行执行的类都接受 `Runnable` 或 `Callable` 作为参数，在 Kotlin 中，你总是可以通过直接的 `{ ... }` lambda 结构替换这种 SAM（单抽象方法）结构，例如：

```
val es = Executors.newFixedThreadPool(10)
// ...
val future = es.submit({
Thread.sleep(2000L)
println("executor over")

} as ()->Int)
val res:Int = future.get()
```

因此，你不必像在 Java 中那样编写：

```
ExecutorService es = Executors.newFixedThreadPool(10);
// ...
Callable c = new Callable() {
public Integer call() {
try {
Thread.sleep(2000L);
} catch(InterruptedException e) { }
System.out.println("executor over");
return 10;
};
Future f = es.submit(c);
int res = f.get();
```

请注意，在 Kotlin 代码中，强制转换为 `()->Int` 是必要的，即使 Android Studio 会抱怨这是多余的。原因在于，如果不这样做，将会调用另一个以 `Runnable` 为参数的方法，导致执行器无法返回值。



## 兼容库

*框架 API* 与*兼容库*之间存在一个很重要但起初不易理解的区别。当你开始开发 Android 应用时，经常会看到不同包中出现同名的类，甚至更糟的是，你还会看到不同包中名称不同的类似乎做着同样的事情。

**注意**

Jetpack 库集合简化了兼容性。如果你使用来自 `androidx.*` 命名空间的 Jetpack 兼容类，就可以忽略下文所述的兼容库。Android Studio 有助于迁移旧项目。`https://developer.android.com/jetpack/androidx/` 上有更多相关信息。

我们来举一个典型的例子：要创建 Activity，你可以继承 `android.app.Activity`，也可以继承 `android.support.v7.app.AppCompatActivity`。查看网上找到的示例和教程，似乎在使用上没有明显区别。实际上，`AppCompatActivity` 继承自 `Activity`，因此任何需要 `Activity` 的地方，你都可以用 `AppCompatActivity` 替代，并且编译也能通过。那么，它们在功能上有区别吗？这取决于具体情况。如果你查看文档或代码，会发现 `AppCompatActivity` 允许添加 `android.support.v7.app.ActionBar`，而 `android.app.Activity` 则不行。相反，`android.app.Activity` 允许添加 `android.app.ActionBar`。而这次 `android.support.v7.app.ActionBar` 并*没有*继承自 `android.app.ActionBar`，所以你不能将 `android.support.v7.app.ActionBar` 添加到 `android.app.Activity` 中。这基本上意味着，如果你倾向于使用 `android.support.v7.app.ActionBar` 而非 `android.app.ActionBar`，那么就必须使用 `AppCompatActivity` 作为 Activity。为什么有人会用 `android.support.v7.app.ActionBar` 而不是 `android.app.ActionBar` 呢？答案很简单：后者相当陈旧——自 API 级别 11 起就已存在。较新版本的 `android.app.ActionBar` 不能破坏 API 以保持与旧设备的兼容性。但 `android.support.v7.app.ActionBar` 可以添加新功能——它更新，自 API 级别 24 起才存在。

其神奇之处如下：如果你使用支持 API 级别 24 或更高版本的设备，你可以使用 `android.support.v7.app.AppCompatActivity` 并添加一个 `android.support.v7.app.ActionBar`。你也可以使用 `android.app.Activity`，但那样就不能添加 `android.support.v7.app.ActionBar`，而必须使用 `android.app.ActionBar`。因此，对于新设备，如果支持库的 ActionBar 比框架的 ActionBar 更适合你的需求，那么为 Activity 使用 `android.support.v7.app.AppCompatActivity` 是合理的。

旧设备呢？你仍然可以使用 `android.support.v7.app.AppCompatActivity`，因为它是作为库添加到应用中的。因此，你也可以使用现代的 `android.support.v7.app.ActionBar` 作为 ActionBar，与设备原生提供的旧版 `android.app.ActionBar` 相比，能获得更多功能。而这正是其中的诀窍所在：*使用支持库，即使是旧设备也能利用以后添加的新功能！* 支持类内部会检查设备版本，并提供合理的回退功能，以尽可能地模拟现代设备。

需要注意的一点是，作为开发者，你必须同时生活在两个世界中：在没有其他选择时，你必须显式或隐式地使用框架类；而在有兼容库可用且你希望确保与旧设备最大程度兼容时，你必须考虑使用兼容库类。因此，在使用某个类之前，检查是否存在匹配的兼容库类至关重要。你可能不喜欢 Android 中这种“两世界”的方法，这也意味着构建应用时需要更多思考工作，但这正是 Android 处理向后兼容性的方式。

如果你在常用搜索引擎中输入“Android support library”，很快就能找到关于支持库的详细信息。

支持库会与应用捆绑在一起，因此必须在构建文件中将其声明为依赖项。如果你在 Android Studio 中启动一个新项目，它默认会在模块的 `build.gradle` 文件中写入以下内容：

```
dependencies {
...
implementation 'com.android.support:appcompat-v7:26.1.0'
implementation 'com.android.support.constraint:constraint-layout:1.0.2'
...
}
```

所以你会看到，支持库 v7 版本默认可用，因此你从一开始就可以使用它。



