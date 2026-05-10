# 第 2 章：响应配置变更

## 默认的配置变更流程

Android 操作系统会持续跟踪其运行设备当前的 `Configuration`（配置）。配置包含多种因素，并且新的配置因素会不断加入。例如，当设备插入扩展坞时，就代表设备配置发生了改变。当 Android 检测到配置变更时，会在正在运行的应用程序中调用回调函数，通知它们变更正在发生，以便应用程序能够正确响应此变更。我们稍后会讨论这些回调，但现在先来回顾一下关于资源的知识。

Android 的一大特色在于，系统会根据设备当前的配置为你的 Activity（活动）选择合适的资源。你无需编写代码来判断当前处于哪种配置；只需通过名称访问资源，Android 就会自动为你获取相应的资源。

如果设备处于竖屏模式，而你的应用请求一个布局，你会得到竖屏布局；如果设备处于横屏模式，则会得到横屏布局。代码只是请求一个布局，而无需指定具体获取哪一种。这一点非常强大，因为即使引入了新的配置因素或配置因素的新取值，代码也无需改变。

开发者需要做的，就是决定是否需要为新的配置创建新的资源，并按要求创建它们。然后，当应用程序经历配置变更时，Android 会向应用提供新的资源，一切功能将继续按预期运行。

出于让事情尽可能简单的强烈愿望，当配置发生变更时，Android 会销毁当前 Activity，并在其位置创建一个新的 Activity。这看起来可能有点粗暴，但实际上并非如此。要让一个正在运行的 Activity 适应变更，需要判断哪些部分保持不变、哪些部分需要更改，然后只处理需要变更的部分，这本身就是一个更大的挑战。

即将被销毁的 Activity 会首先得到适当的通知，让你有机会保存任何需要保存的内容。当新的 Activity 被创建时，它有机会利用前一个 Activity 的数据来恢复状态。为了获得良好的用户体验，显然你希望这个保存和恢复过程不要花费太长时间。

只要应用及其 Activity 的设计方式能确保它们不包含大量需要长时间重新创建的非 UI 内容，那么保存你需要保存的数据，然后让 Android 丢弃其余部分并重新开始，是相当容易的。这恰恰是成功进行配置变更设计的关键：不要把那些在配置变更期间难以轻松重新创建的"东西"放在 Activity 内部。

请记住，我们的应用程序并没有被销毁，因此处于应用上下文（而非当前 Activity）中的任何内容，对于新 Activity 来说仍然存在。单例对象仍然可用，我们为应用程序派生出的任何后台线程也仍然存在。

我们正在使用的任何数据库或内容提供程序也仍然存在。利用好这些特性，可以使配置变更变得快速且无痛。如果可能，尽量将数据和业务逻辑放在 Activity 之外。

配置变更的流程在 Activity 和 Fragment（片段）之间有些相似。当一个 Activity 被销毁并重新创建时，该 Activity 内的所有 Fragment 也会被销毁并重新创建。那么我们需要担心的，就是关于 Fragment 和 Activity 的状态信息，例如当前显示给用户的数据，或者我们希望保留的内部值。我们将保存想要保留的内容，并在 Fragment 和 Activity 被重新创建时，在另一边重新拾取它们。



## 在配置变更期间保护数据

你需要保护那些不易重新创建的数据，避免它们在默认的配置变更过程中被销毁。

### Activity 的销毁/重建周期

在处理 Activity 的默认配置变更时，需要注意三个回调方法：

- `onSaveInstanceState()`
- `onCreate()`
- `onRestoreInstanceState()`

第一个回调是 Android 在检测到配置变更发生时将调用的方法。Activity 有机会保存其状态，以便在配置变更结束时创建的新 Activity 能够恢复这些状态。`onSaveInstanceState()` 回调将在调用 `onStop()` 之前被调用。此时可以访问任何存在的状态，并将其保存到 `Bundle` 对象中。当 Activity 被重新创建时，这个对象将被传递给另外两个回调方法（`onCreate()` 和 `onRestoreInstanceState()`）。你只需要在其中一个回调方法中添加逻辑来恢复 Activity 的状态即可。

默认的 `onSaveInstanceState()` 回调会为你做一些有用的事情。例如，它会遍历当前活跃的视图层级，并保存每个带有 `android:id` 的视图的值。这意味着如果你有一个接收了用户输入的 `EditText` 视图，那么该输入在 Activity 销毁/重建周期后仍然可用，以便在用户重新获得控制权之前填充 `EditText`。你无需手动逐个保存这些状态。如果你确实重写了 `onSaveInstanceState()`，请确保使用 bundle 对象调用 `super.onSaveInstanceState()`，这样系统就能为你处理这些工作。保存的不是视图本身，而是那些需要在销毁/重建边界间保持不变的视图状态属性。

[www.it-ebooks.info](http://www.it-ebooks.info/)

要将数据保存到 bundle 对象中，请使用诸如用于整数的 `putInt()` 和用于字符串的 `putString()` 之类的方法。`android.os.Bundle` 类中有相当多的方法；你的选择不限于整数和字符串。例如，`putParcelable()` 可用于保存复杂对象。每个 `put` 方法都使用一个字符串键，之后你将使用相同的键来检索该值。一个示例性的 `onSaveInstanceState()` 可能如代码清单 2-1 所示。

**代码清单 2-1. 示例 `onSaveInstanceState()`**

```java
@Override
public void onSaveInstanceState(Bundle icicle) {
    super.onSaveInstanceState(icicle);
    icicle.putInt("counter", 1);
}
```

有时 bundle 被称为 `icicle`，因为它代表 Activity 的一个冻结的小片段。在这个示例中，你仅保存了一个值，它的键是 `counter`。你可以通过在此回调中添加更多 `put` 语句来保存更多值。此示例中的 `counter` 值有些临时性，因为如果应用被完全销毁，当前值将会丢失。例如，当用户关闭设备时就会发生这种情况。在第 4 章中，你将学习更持久地保存值的方法。此实例状态仅用于在应用本次运行期间保留值。不要将此机制用于需要长期保留的重要状态。

要恢复 Activity 状态，你需要访问 bundle 对象以检索你认为存在的值。同样，你需要使用 `Bundle` 类的方法，如 `getInt()` 和 `getString()`，并传入相应的键来指定你想要的值。如果该键在 `Bundle` 中不存在，则会返回 0 或 null（取决于所请求对象的类型）。或者，你可以在相应的 getter 方法中提供一个默认值。

代码清单 2-2 展示了一个示例性的 `onRestoreInstanceState()` 回调。

**代码清单 2-2. 示例 `onRestoreInstanceState()`**

```java
@Override
public void onRestoreInstanceState(Bundle icicle) {
    super.onRestoreInstanceState(icicle);
    int someInt = icicle.getInt("counter", -1);
    // 现在使用 someInt 来恢复 Activity 的状态。
    // 如果未找到值，则 -1 为默认值。
}
```

[www.it-ebooks.info](http://www.it-ebooks.info/)

你可以在 `onCreate()` 或 `onRestoreInstanceState()` 中恢复状态，这取决于你。许多应用会在 `onCreate()` 中恢复状态，因为那里是执行大量初始化工作的地方。将两者分开的一个原因是，如果你正在创建一个可能被扩展的 Activity 类。进行扩展的开发者可能会发现，只需重写 `onRestoreInstanceState()` 并添加恢复状态的代码，比必须重写整个 `onCreate()` 要更容易。

这里非常重要的一点是，你需要注意对 Activity、视图以及其他对象的引用，这些对象需要在当前 Activity 被完全销毁时能够被垃圾回收。如果你将某些引用了将要被销毁的 Activity 的对象放入保存的 bundle 中，那么该 Activity 将无法被垃圾回收。这很可能会导致内存泄漏，并且泄漏可能会不断增长，直到你的应用崩溃。

应避免放入 bundle 的对象包括 `Drawables`、`Adapters`、`Views` 以及任何与 Activity 上下文绑定的其他内容。不要将 `Drawable` 直接放入 bundle，而是应该序列化位图并保存它。或者更好的是，在 Activity 和 Fragment 外部管理位图，而不是在内部管理。向 bundle 中添加某种对位图的引用。当需要为新 Fragment 重新创建任何 `Drawables` 时，使用该引用来访问外部位图以重新生成你的 `Drawables`。

### Fragment 的销毁/重建周期

Fragment 的销毁/重建周期与 Activity 非常相似。一个正在被销毁并重建的 Fragment 会调用其 `onSaveInstanceState()` 回调，允许 Fragment 将值保存到 `Bundle` 对象中以备后用。一个区别是，当 Fragment 被重新创建时，有六个 Fragment 回调会接收这个 `Bundle` 对象：`onInflate()`、`onCreate()`、`onCreateView()`、`onActivityCreated()`、`onViewCreated()` 和 `onViewStateRestored()`。最后两个回调是较新的，分别来自 Honeycomb 3.2 和 JellyBean 4.2。这为我们提供了大量机会，可以根据重建的 Fragment 的先前状态来重建其内部状态。

Android 仅保证在 `onDestroy()` 之前的某个时间点会为 Fragment 调用 `onSaveInstanceState()`。这意味着当调用 `onSaveInstanceState()` 时，视图层次可能已附加，也可能尚未附加。因此，不要指望在 `onSaveInstanceState()` 内部遍历视图层次。例如，如果 Fragment 位于 Fragment 回退栈中，则不会显示任何 UI，因此不会存在任何视图层次。这当然没问题，因为如果没有 UI 显示，就无需尝试捕获视图的当前值来保存它们。在尝试保存视图的当前值之前，你需要检查视图是否存在，并且不要将视图不存在视为错误。

[www.it-ebooks.info](http://www.it-ebooks.info/)

与 Activity 类似，注意不要在 bundle 对象中包含那些引用了在此 Fragment 被重新创建时可能不存在的 Activity 或 Fragment 的项。尽量保持 bundle 的大小尽可能小，并尽可能将长期存在的数据存储在 Activity 和 Fragment 之外，仅从你的 Activity 和 Fragment 中引用这些数据。这样，你的销毁/重建周期会快得多，产生内存泄漏的可能性也会大大降低，并且你的 Activity 和 Fragment 代码应该更容易维护。

### 使用 FragmentManager 保存 Fragment 状态

Fragment 还有另一种保存状态的方法，可以作为……的补充



## 处理配置变化

Android 不会通知 `Fragment` 保存其状态。在 Honeycomb 3.2 中，`FragmentManager` 类新增了 `saveFragmentInstanceState()` 方法，可调用该方法生成 `Fragment.SavedState` 类的对象。前面章节中提到的保存状态的方法都是在 Android 内部进行的。

虽然我们知道状态正在被保存，但我们无法直接访问它。这种保存状态的方法会生成一个代表 `Fragment` 保存状态的对象，并允许你控制是否以及何时从该状态创建 `Fragment`。

使用 `Fragment.SavedState` 对象恢复 `Fragment` 的方法是通过 `Fragment` 类的 `setInitialSavedState()` 方法。在第 1 章中，你了解到最好使用静态工厂方法（例如 `newInstance()`）创建新的 `Fragment`。在这个方法中，你看到了如何调用默认构造函数，然后附加一个参数包。你可以改为调用 `setInitialSavedState()` 方法来设置它，以便恢复到之前的状态。

关于这种保存 `Fragment` 状态的方法，有几个注意事项需要了解：

*   要保存的 `Fragment` 当前必须已附加到 `FragmentManager`。
*   使用此保存状态创建的新 `Fragment` 必须与创建它的 `Fragment` 属于相同的类类型。
*   保存的状态不能包含对其他 `Fragment` 的依赖。当保存的 `Fragment` 被重新创建时，其他 `Fragment` 可能不存在。

[www.it-ebooks.info](http://www.it-ebooks.info/)

## 在 Fragment 上使用 setRetainInstance

`Fragment` 可以避免在配置更改时被销毁和重新创建。如果使用参数 `true` 调用 `setRetainInstance()` 方法，则当它的 `Activity` 被销毁并重新创建时，该 `Fragment` 将保留在应用程序中。不会调用 `Fragment` 的 `onDestroy()` 回调，也不会调用 `onCreate()`。`onDetach()` 回调会被调用，因为 `Fragment` 必须与即将消失的 `Activity` 分离；而 `onAttach()` 和 `onActivityCreated()` 会被调用，因为 `Fragment` 已附加到新的 `Activity`。这仅适用于不在返回栈上的 `Fragment`。对于没有 UI 的 `Fragment` 尤其有用。

此功能非常强大，因为你可以使用非 UI 的 `Fragment` 来处理对数据对象和后台线程的引用，并在该 `Fragment` 上调用 `setRetainInstance(true)`，以便它不会在配置更改时被销毁和重新创建。额外的好处是，在正常的配置更改过程中，非 UI `Fragment` 的回调 `onDetach()` 和 `onAttach()` 会将 `Activity` 引用从旧 `Activity` 切换到新 `Activity`。

### 已弃用的配置更改方法

`Activity` 上的几个方法已被弃用，因此不应再使用它们：

*   `getLastNonConfigurationInstance()`
*   `onRetainNonConfigurationInstance()`

这些方法以前允许你保存一个正在被销毁的 `Activity` 中的任意对象，并将其传递给正在被创建的 `Activity` 的下一个实例。虽然它们很有用，但现在你应该使用前面描述的方法来管理销毁/创建周期中 `Activity` 实例之间的数据。

### 自行处理配置更改

到目前为止，你已经了解了 Android 如何为你处理配置更改。它负责销毁和重新创建 `Activity` 和 `Fragment`，为新的配置拉取最佳资源，保留任何用户输入的数据，并让你有机会在某些回调中执行一些额外的逻辑。这通常是你最好的选择。但当情况并非如此，当你必须自行处理配置更改时，Android 提供了一种出路。

[www.it-ebooks.info](http://www.it-ebooks.info/)

不推荐这样做，因为完全由你来确定由于更改而需要更改的内容，然后你需要负责进行所有更改。如前所述，除了方向变化之外，还有许多其他配置更改。幸运的是，你不一定需要自行处理所有配置更改。

自行处理配置更改的第一步是在 `AndroidManifest.xml` 文件的 `<activity>` 标签中，使用 `android:configChanges` 属性声明你将处理哪些更改。Android 将使用前面描述的方法处理其他配置更改。你可以通过使用 `|` 符号将它们组合在一起，根据需要指定任意数量的配置更改类型，如下所示：

```
<activity ... android:configChanges="orientation|keyboardHidden" ... >
```

配置更改类型的完整列表可以在 `R.attr` 的参考页面上找到。请注意，如果你的目标 API 是 13 或更高，并且你需要处理方向更改，则还需要处理 `screenSize`。

配置更改的默认过程是调用回调来销毁和重新创建 `Activity` 或 `Fragment`。当你声明将处理特定的配置更改时，过程会发生变化，因此仅调用 `onConfigurationChanged()` 回调（在 `Activity` 及其 `Fragment` 上）。Android 会传入一个 `Configuration` 对象，以便回调知道新的配置是什么。由回调来确定可能发生了什么变化；但是，由于你可能只自行处理少量的配置更改，因此弄清楚这一点应该不会太难。

你只有在需要完成的工作非常少，并且可以跳过销毁和重新创建时，才真正希望自行处理配置更改。例如，如果竖屏和横屏的 `Activity` 布局相同，并且所有图像资源都相同，那么销毁和重新创建 `Activity` 实际上并不能带来任何好处。在这种情况下，声明你将处理方向配置更改是相当安全的。在你的 `Activity` 方向更改期间，`Activity` 将保持不变，只需使用现有资源（如布局、图像、字符串等）在新的方向上重新渲染自身。但如果可以的话，让 Android 来处理这些事情其实也没什么大不了的。

[www.it-ebooks.info](http://www.it-ebooks.info/)

## 参考资料

以下是一些可能有用的参考资料，供你进一步探索相关主题：

*   [www.androidbook.com/androidfragments/projects](http://www.androidbook.com/androidfragments/projects)：本书相关可下载项目的列表。对于本章，请查找名为 `AndroidFragments_Ch02_ConfigChanges.zip` 的 ZIP 文件。此 ZIP 文件包含本章的所有项目，这些项目列在不同的根目录中。还有一个 `README.TXT` 文件，准确描述了如何从这些 ZIP 文件之一将项目导入到你的 IDE 中。
*   [`developer.android.com/guide/topics/fundamentals/activities.html#SavingActivityState`](http://developer.android.com/guide/topics/fundamentals/activities.html#SavingActivityState)：Android 开发者指南，讨论了保存和恢复状态。
*   [`developer.android.com/guide/topics/resources/runtime-changes.html`](http://developer.android.com/guide/topics/resources/runtime-changes.html)：Android 处理运行时变更的 API 指南。

## 总结

让我们通过快速列举你学到的关于处理配置更改的知识来结束本章：

*   默认情况下，`Activity` 在配置更改期间会被销毁并重新创建。`Fragment` 也是如此。
*   避免将大量数据和逻辑放入 `Activity` 中，以便配置更改能够快速发生。



