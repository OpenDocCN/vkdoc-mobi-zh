# Fragment 基础

需要特别注意的是，`Fragment`类有一个`getActivity()`方法，它会返回该`Fragment`所依附的`Activity`。请记住，在整个生命周期中，你可以通过`Fragment`的`getArguments()`方法获取初始化参数`Bundle`。然而，一旦`Fragment`被附加到其`Activity`上，就不能再次调用`setArguments()`。因此，除了在最开始的时候，你不能再向初始化参数中添加内容。

## `onCreate()`回调

接下来是`onCreate()`回调。虽然这与`Activity`的`onCreate()`类似，但区别在于你不应该在此处放置依赖`Activity`视图层次结构的代码。此时你的`Fragment`可能已经与其`Activity`关联，但你尚未收到`Activity`的`onCreate()`已完成的通知。这将在后续步骤中发生。如果有保存的状态`Bundle`，此回调会将其传入。这个回调是创建后台线程以获取`Fragment`所需数据的最早时机。你的`Fragment`代码运行在 UI 线程上，你不希望在 UI 线程上执行磁盘输入/输出（I/O）或网络访问。事实上，启动一个后台线程来准备数据是非常合理的。后台线程应该处理阻塞调用。你之后需要与数据建立连接，可能使用`Handler`或其他技术。

## `onCreateView()`回调

下一个回调是`onCreateView()`。这里期望你返回此`Fragment`的视图层次结构。传入此回调的参数包括一个`LayoutInflater`（可用于为此`Fragment`填充布局）、一个`ViewGroup`父容器（在**清单 1-2**中称为`container`），以及保存的`Bundle`（如果存在）。非常重要的一点是，你不应该将视图层次结构附加到传入的`ViewGroup`父容器上。该关联稍后将自动发生。如果你在此回调中将`Fragment`的视图层次结构附加到父容器上，很可能会引发异常——或者至少会出现奇怪且意外的应用程序行为。

**清单 1-2 在`onCreateView()`中创建 Fragment 视图层次结构**

```java
@Override
public View onCreateView(LayoutInflater inflater,
                         ViewGroup container, Bundle savedInstanceState) {
    if(container == null)
        return null;

    View v = inflater.inflate(R.layout.details, container, false); 
    TextView text1 = (TextView) v.findViewById(R.id.text1);
    text1.setText(myDataSet[ getPosition() ] );
    return v;
}
```

提供父容器的目的是让你能够将其与`LayoutInflater`的`inflate()`方法一起使用。如果父容器值为`null`，则意味着此特定`Fragment`将不会被显示，因为没有要附加的视图层次结构。在这种情况下，你可以直接从此处返回`null`。请记住，你的应用程序中可能存在一些未被显示的`Fragment`。

**清单 1-2**展示了你可能在此方法中执行的操作示例。你可以看到如何访问仅用于此`Fragment`的布局 XML 文件，并将其填充为视图，然后返回给调用者。这种方法有几个优点。你始终可以在代码中构建视图层次结构，但通过填充布局 XML 文件，你可以利用系统的资源查找逻辑。根据设备所处的配置，或者根据你使用的设备，系统会选择适当的布局 XML 文件。然后你可以访问布局中的特定视图（在此例中为`text1`的`TextView`字段）来执行所需操作。重复一个非常重要的要点：不要在此回调中将`Fragment`的视图附加到父容器上。你可以在**清单 1-2**中看到，你在调用`inflate()`时使用了容器，但同时你也为`attachToRoot`参数传递了`false`。



### `onViewCreated()` 回调

该方法在 `onCreateView()` 之后、但在任何已保存状态被注入 UI 之前立即调用。传入的视图对象与 `onCreateView()` 返回的视图对象是同一个。

### `onActivityCreated()` 回调

现在已接近用户可以与你片段交互的阶段。下一个回调是 `onActivityCreated()`，它在 Activity 完成其 `onCreate()` 回调后被调用。此时你可以确信，Activity 的视图层级结构（如果你之前返回了视图，则包括你自己的视图层级结构）已准备就绪并可用了。你可以在此处对用户界面进行最终调整，这些调整将在用户看到界面之前完成。同时，你也可以在此处确认，该 Activity 的其他任何片段都已附着到你的 Activity 上。

### `onViewStateRestored()` 回调

该回调相对较新，于 JellyBean 4.2 中引入。当该片段的视图层级结构的所有状态都已恢复（如果适用）时，你的片段将收到此回调。以前你必须在 `onActivityCreated()` 中针对恢复的片段决定是否调整 UI。现在，你可以将该逻辑放在此回调中，因为你确切地知道该片段正在从已保存的状态中被恢复。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**第 1 章：片段基础**

### `onStart()` 回调

片段生命周期中的下一个回调是 `onStart()`。此时，你的片段对用户可见，但你尚未开始与用户交互。此回调与 Activity 的 `onStart()` 相关联。因此，尽管以前你可能将逻辑放入 Activity 的 `onStart()` 中，但现在你更可能将逻辑放入片段的 `onStart()` 中，因为这也是用户界面组件所在的位置。

### `onResume()` 回调

用户能与你的片段交互之前的最后一个回调是 `onResume()`。此回调与 Activity 的 `onResume()` 相关联。当此回调返回时，用户便可以自由地与这个片段交互。例如，如果你的片段中有一个相机预览，你可能会在片段的 `onResume()` 中启用它。

至此，你的应用已进入让用户满意的忙碌阶段。接着，用户决定离开你的应用，可能是通过返回键退出、按 Home 键，或启动其他应用。与 Activity 发生的情况类似，接下来的序列会进入与设置片段交互相反的方向。

### `onPause()` 回调

片段的第一个撤销回调是 `onPause()`。此回调与 Activity 的 `onPause()` 相关联；与 Activity 一样，如果你的片段中有媒体播放器或其他共享对象，你可以在 `onPause()` 方法中暂停它、停止它或将其归还。同样适用好公民规则：如果用户正在接听电话，你不想播放音频。

### `onSaveInstanceState()` 回调

与 Activity 类似，片段有机会保存状态以供日后重建。此回调会传入一个 `Bundle` 对象，作为你想保留的任何状态信息的容器。这就是之前介绍的回调中传递的已保存状态 Bundle。

为防止内存问题，请谨慎选择保存到该 Bundle 中的内容。只保存你需要的内容。如果你需要保留对另一个片段的引用，不要试图保存或放置该片段，而只需保存该片段的标识符，如它的标签或 ID。当该片段运行 `onViewStateRestored()` 时，你可以重新建立该片段所依赖的其他片段的连接。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**第 1 章：片段基础**

尽管你可能会看到该方法通常在 `onPause()` 之后立即被调用，但此片段所属的 Activity 会在它认为应该保存片段状态时调用它。这可以发生在 `onDestroy()` 之前的任何时间。

### `onStop()` 回调



