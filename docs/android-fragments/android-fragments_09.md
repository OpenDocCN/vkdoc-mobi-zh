# 第 1 章：Fragment 基础

这个 fragment 与之前的详情 fragment 之间的另一个区别在于：当这个 fragment 被销毁并重新创建时，你会将状态（即列表中的当前选中位置值）保存在一个 bundle 中，并在 `onCreate()` 中将其读取回来。与那些在 Activity 布局的 `FrameLayout` 中被频繁替换的详情 fragment 不同，这里只需要考虑一个标题 fragment。因此，当发生配置变更，你的标题 fragment 经历保存和恢复操作时，你希望记住它之前的位置。对于详情 fragment，你可以重新创建它们，而无需记住前一个状态。

## 在必要时调用独立的 Activity

有一段代码我们还没有讨论过，它出现在 `showDetails()` 中，针对的是竖屏模式下详情 fragment 无法与标题 fragment 合适地同屏显示的情况。如果屏幕空间不允许将一个本应与其他 fragment 一同显示的 fragment 以可用的方式呈现，那么你需要启动一个独立的 Activity 来展示该 fragment 的用户界面。对于你的示例应用，你可以实现一个详情 Activity；代码见列表 1-10。

*列表 1-10. 当 Fragment 无法适配时显示一个新的 Activity*
```java
public class DetailsActivity extends Activity {
    @Override
    public void onCreate(Bundle savedInstanceState) {
```


以下是根据您的要求，对提供的英文文本进行的中文翻译，已严格遵循所有格式注意事项。


### 日志片段生命周期示例

`Log.v(MainActivity.TAG, "in DetailsActivity onCreate"); super.onCreate(savedInstanceState);`

```
if (getResources().getConfiguration().orientation == Configuration.ORIENTATION_LANDSCAPE) {
    // 如果屏幕当前处于横屏模式，意味着
    // 你的 MainActivity 同时显示了标题和文本，
    // 因此这个 Activity 不再需要。
    // 退出并让 MainActivity 处理所有工作。
    finish();
    return;
}

if (getIntent() != null) {
    // 这是实例化细节片段的另一种方式。
    DetailsFragment details = DetailsFragment.newInstance(getIntent().getExtras());
    getFragmentManager().beginTransaction()
        .add(android.R.id.content, details)
        .commit();
}
```

这段代码有几个有趣的地方。首先，它非常容易实现。你只需简单判断设备的方向，当处于竖屏模式时，你在这个细节 Activity 中创建一个新的细节片段。如果处于横屏模式，你的 `MainActivity` 能够同时显示标题片段和细节片段，因此完全没有理由显示这个 Activity。你可能会疑惑，既然处于横屏模式，为什么还要启动这个 Activity？答案是：你不会这么做。然而，一旦这个 Activity 在竖屏模式下启动，如果用户将设备旋转到横屏模式，由于配置变更，这个细节 Activity 会重新启动。如此一来，Activity 在启动时就会处于横屏模式。此时，合理的做法是结束这个 Activity，让 `MainActivity` 接管所有工作。

这个细节 Activity 的另一个有趣之处在于，你从未使用 `setContentView()` 设置根内容视图。那么用户界面是如何创建的呢？仔细观察片段事务中的 `add()` 方法调用，你会发现添加片段的视图容器被指定为资源 `android.R.id.content`。这是 Activity 的顶级视图容器，因此当你将片段视图层次结构附加到这个容器时，片段视图层次结构就成为了 Activity 唯一的视图层次结构。你使用了与之前相同的 `DetailsFragment` 类，并通过另一个 `newInstance()` 方法（接受一个 Bundle 作为参数）创建片段，然后将其简单地附加到 Activity 视图层次结构的顶部。这使得该片段在这个新的 Activity 中得以显示。

从用户的角度来看，他们现在看到的只是细节片段的视图，也就是莎士比亚戏剧中的文本。如果用户想选择不同的标题，他们按下返回键，这会弹出这个 Activity，从而显示你的主 Activity（仅包含标题片段）。用户另一个选择是旋转设备回到横屏模式。这时你的细节 Activity 会调用 `finish()` 并消失，显示出下面也同样旋转后的主 Activity。

当设备处于竖屏模式时，如果你不在主 Activity 中显示细节片段，你应该有一个单独的竖屏模式 `main.xml` 布局文件，就像清单 1-11 所示。

**清单 1-11. 竖屏主 Activity 的布局**

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <fragment class="com.androidbook.fragments.bard.TitlesFragment"
        android:id="@+id/titles"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />
</LinearLayout>
```

当然，你可以根据需要自由设置这个布局。在此处，你的目的仅仅是让它单独显示标题片段。你的标题片段类不需要包含太多代码来处理设备重新配置，这非常棒。

花点时间查看一下这个应用程序的清单文件。在其中，你会发现主 Activity 的类别为 `LAUNCHER`，这样它就会出现在设备的应用列表中。然后，你有一个单独的 `DetailsActivity`，其类别为 `DEFAULT`。这允许你通过代码启动细节 Activity，但细节 Activity 不会作为一个应用出现在应用列表中。

### 片段的持久化

当你使用这个示例应用程序时，请务必旋转设备（按 `Ctrl+F11` 可在模拟器中旋转设备）。你会看到设备旋转，片段也随之旋转。如果你观察 LogCat 消息，你会看到很多关于这个应用程序的日志。

具体来说，在设备旋转期间，请仔细关注关于片段的日志消息；不仅 Activity 被销毁并重建，片段也是如此。

到目前为止，你只在标题片段中编写了一小段代码，用于在重启后记住标题列表中的当前位置。你没有在细节片段代码中做任何处理配置变更的事情，这是因为你不需要这样做。Android 会负责保留在片段管理器中的片段，在 Activity 被重建时保存它们，然后恢复它们。你应该意识到，重新配置完成后你拿到的片段，很可能不是内存中之前的那些片段。这些片段已经为你重建。Android 保存了参数 bundle 和片段类型的记录，并为每个片段存储了包含状态保存信息的 saved-state bundle，以便在另一端恢复片段。

LogCat 消息显示了片段与 Activity 同步经历其生命周期的过程。你会看到细节片段被重建，但你的 `newInstance()` 方法并未再次被调用。相反，Android 使用默认构造函数，将参数 bundle 附加到它上面，然后开始对片段调用回调函数。这就是为什么不要在 `newInstance()` 方法中做任何花哨操作如此重要的原因：当片段被重建时，它不会通过 `newInstance()` 进行。

到目前为止，你还应该意识到你已经在几个不同的地方重用了你的片段。标题片段在两个不同的布局中使用，但如果你查看标题片段代码，它并不关心每个布局的属性。你可以让这两个布局彼此相当不同，但标题片段的代码看起来是一样的。

细节片段也是如此。它在主横屏布局中以及细节 Activity 中被独立使用。同样，细节片段的布局在这两个地方可能非常不同，但细节片段的代码是相同的。细节 Activity 的代码也非常简单。

到目前为止，你已经探索了两种片段类型：基本的 `Fragment` 类和 `ListFragment` 子类。Fragment 还有其他子类：`DialogFragment`、`PreferenceFragment` 和 `WebViewFragment`。我们将在第 3 章和第 4 章分别介绍 `DialogFragment` 和 `PreferenceFragment`。

### 与片段的通信



由于 fragment 管理器知道所有附加到当前 activity 的 fragment，因此 activity 或该 activity 中的任何 fragment 都可以使用前面描述的 getter 方法请求任何其他 fragment。一旦获得 fragment 引用，activity 或 fragment 可以适当地转换该引用，然后直接在该 activity 或 fragment 上调用方法。这会导致你的 fragment 对其它 fragment 的了解比通常预期的要多，但不要忘记你是在移动设备上运行此应用程序，因此走些捷径有时是合理的。代码片段如代码清单 1-12 所示，展示了某个 fragment 如何直接与另一个 fragment 通信。

该片段将是你的某个扩展 `Fragment` 类的一部分，而 `FragmentOther` 则是另一个不同的扩展 `Fragment` 类。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**32**

**第 1 章：Fragment 基础**

*代码清单 1-12. 直接的 Fragment 到 Fragment 通信*

```
FragmentOther fragOther =
    (FragmentOther)getFragmentManager().findFragmentByTag("other");
fragOther.callCustomMethod( arg1, arg2 );
```

在代码清单 1-12 中，当前 fragment 直接了解另一个 fragment 的类以及该类上存在哪些方法。这可能没问题，因为这些 fragment 是同一个应用程序的一部分，简单地接受某些 fragment 会了解其他 fragment 这一事实可能更容易。我们将在第 3 章的 `DialogFragment` 示例应用程序中展示一种更清晰的 fragment 间通信方式。

**使用 `startActivity()` 和 `setTargetFragment()`**

Fragment 一个与 activity 非常相似的特性是它能够启动一个 activity。`Fragment` 具有 `startActivity()` 方法和 `startActivityForResult()` 方法。它们的工作方式与 activity 的对应方法相同；当传回结果时，它将导致启动该 activity 的 fragment 上的 `onActivityResult()` 回调被触发。

还有另一种你应该了解的通信机制。当一个 fragment 想要启动另一个 fragment 时，有一个特性允许调用 fragment 向被调用 fragment 设置其身份。代码清单 1-13 展示了一个可能看起来是什么样的示例。

*代码清单 1-13. Fragment 到目标 Fragment 的设置*

```
mCalledFragment = new CalledFragment();
mCalledFragment.setTargetFragment(this, 0);
fm.beginTransaction().add(mCalledFragment, "work").commit();
```

通过这几行代码，你创建了一个新的 `CalledFragment` 对象，将被调用 fragment 上的目标 fragment 设置为当前 fragment，并使用 fragment 事务将被调用 fragment 添加到 fragment 管理器和 activity。当被调用 fragment 开始运行时，它将能够调用 `getTargetFragment()`，该方法将返回对调用 fragment 的引用。有了这个引用，被调用 fragment 可以在调用 fragment 上调用方法，甚至直接访问视图组件。例如，在代码清单 1-14 中，被调用 fragment 可以直接在调用 fragment 的 UI 中设置文本。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**第 1 章：Fragment 基础**

**33**

*代码清单 1-14. 目标 Fragment 到 Fragment 的通信*

```
TextView tv = (TextView)
    getTargetFragment().getView().findViewById(R.id.text1);
tv.setText("Set from the called fragment");
```

**参考资料**

以下是一些可能有用的参考资料，供你进一步探索相关主题：

- [www.androidbook.com/androidfragments/projects](http://www.androidbook.com/androidfragments/projects)：一个与本书相关的可下载项目列表。名为 `AndroidFragments_Ch01_Fragments.zip` 的文件包含本章的所有项目，这些项目列在单独的根目录中。还有一个 `README.TXT` 文件，描述了如何从这些 zip 文件之一将项目导入 IDE。它还包含一些使用面向旧 Android 版本的 Fragment 兼容性 SDK 的项目。
- [`developer.android.com/guide/components/fragments.html`](http://developer.android.com/guide/components/fragments.html)



