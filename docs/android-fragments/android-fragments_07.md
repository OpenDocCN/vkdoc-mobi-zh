# 片段生命周期回调

### `onStop()` 回调

下一个撤销回调是 `onStop()`。该回调与 Activity 的 `onStop()` 绑定，其作用与 Activity 的 `onStop()` 类似。已停止的片段可以直接回到 `onStart()` 回调，随后进入 `onResume()`。

### `onDestroyView()` 回调

如果你的片段即将被销毁或保存，撤销方向的下一个回调是 `onDestroyView()`。此回调将在你先前在 `onCreateView()` 中创建的视图层次结构从片段中分离后被调用。

### `onDestroy()` 回调

接下来是 `onDestroy()`。当片段不再被使用时，会调用此方法。

请注意，此时片段仍附着在 Activity 上且仍可被找到，但它已无法执行太多操作。

### `onDetach()` 回调

片段生命周期的最后一个回调是 `onDetach()`。一旦调用该回调，片段便与 Activity 解除绑定，不再拥有视图层次结构，且其所有资源应已被释放。

## 使用 `setRetainInstance()`

你可能已经注意到图 1-2 中的虚线。片段的一个巧妙特性是，当 Activity 被重新创建（因此片段也会随之重建）时，你可以指定不希望片段被完全销毁。因此，Fragment 提供了一个名为 `setRetainInstance()` 的方法，该方法接受一个布尔参数来指示：“是的，我希望在 Activity 重启时保留该片段”或“不，请销毁它，我将从头创建一个新片段”。

调用 `setRetainInstance()` 的最佳位置是片段的 `onCreate()` 回调，但 `onCreateView()` 和 `onActivityCreated()` 中也可以调用。

[www.it-ebooks.info](http://www.it-ebooks.info/)

**第 1 章：片段基础**

如果参数为 true，则表示你希望将片段对象保留在内存中，而不是从头重新创建。然而，如果 Activity 正在被销毁并重新创建，你必须将片段从当前 Activity 分离，并将其附着到新的 Activity 上。关键点在于：如果保留了实例值为 true，你实际上不会销毁片段实例，因此在另一端也无需创建新实例。图中的虚线意味着在销毁流程中会跳过 `onDestroy()` 回调，在片段重新附着到新 Activity 时会跳过 `onCreate()` 回调，而所有其他回调仍会触发。

由于 Activity 重新创建通常由配置变更引起，你的片段回调应假定配置已发生变更并采取相应措施。例如，在 `onCreateView()` 中加载布局以创建新的视图层次结构。清单 1-2 中提供的代码已按此方式编写，可处理该情况。如果你选择使用保留实例功能，可以将部分初始化逻辑放在 `onCreate()` 之外，因为它不会像其他回调那样总是被调用。

## 展示生命周期的片段示例应用

没有什么比查看真实示例更能理解一个概念了。我们将使用一个经过插桩的示例应用，让你观察所有这些回调的实际运行。你将使用一个示例应用，该应用在一个片段中显示莎士比亚作品标题列表；当用户点击某个标题时，该剧作的文本会出现在另一个片段中。此示例应用可在平板电脑的横屏和竖屏模式下运行。随后，你将配置它以模拟在较小屏幕上运行，从而了解如何将文本片段分离到一个 Activity 中。

首先，我们从清单 1-3 中 Activity 的横屏模式 XML 布局开始，运行时它看起来将如图 1-3 所示。

**清单 1-3.** Activity 的横屏模式布局 XML

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    android:orientation="horizontal"
    android:layout_width="match_parent"
```



`android:layout_height="match_parent"`>

```xml
<fragment class="com.androidbook.fragments.bard.TitlesFragment"
    android:id="@+id/titles" android:layout_weight="1"
    android:layout_width="0px"
    android:layout_height="match_parent" />
```

`<FrameLayout
    android:id="@+id/details" android:layout_weight="2"
    android:layout_width="0px"
    android:layout_height="match_parent" />
</LinearLayout>`

**图 1-3.** 示例片段应用程序的用户界面

**注意** 本章末尾提供了可用于下载本章项目的 URL。这将允许您将这些项目直接导入到 IDE（如 Eclipse 或 Android Studio）中。

这个布局看起来与全书看到的许多其他布局类似，水平方向从左到右排列着两个主要对象。不过，这里有一个名为 `<fragment>` 的新标签，并且这个标签有一个名为 `class` 的新属性。

请记住，片段不是一个视图，因此片段在布局 XML 中的写法与其他元素略有不同。另一件需要记住的事情是，`<fragment>` 标签在这个布局中只是一个占位符。您不应该在布局 XML 文件中的 `<fragment>` 标签下放置子标签。

片段的其他属性看起来都很熟悉，其用途与视图的类似。片段标签的 `class` 属性指定了应用程序标题的扩展类。也就是说，您必须扩展 Android Fragment 类之一来实现您的逻辑，而 `<fragment>` 标签必须知道您扩展类的名称。片段有自己的视图层次结构，稍后由片段本身创建。下一个标签是 `FrameLayout`——而不是另一个 `<fragment>` 标签。这是为什么呢？我们稍后会更详细地解释，但就目前而言，您应该意识到您将要对文本进行一些转换，将一个片段替换为另一个片段。

您使用 `FrameLayout` 作为视图容器来容纳当前的文本片段。对于您的标题片段，您只需要关心一个片段：无需替换，无需转换。对于显示莎士比亚文本的区域，您将会有多个片段。

`MainActivity` Java 代码如代码清单 1-4 所示。实际上，该清单只显示了有趣的代码。代码中插入了日志消息，以便您可以通过 LogCat 查看发生了什么。请从网站查看 `ShakespeareInstrumented` 的源代码文件以了解全部内容。

*代码清单 1-4. **MainActivity** 中有趣的源代码*

```java
public boolean isMultiPane() {
    return getResources().getConfiguration().orientation
            == Configuration.ORIENTATION_LANDSCAPE;
}

/**
 * 辅助函数，用于显示选中项目的详细信息，方法为：
 * 在当前 UI 中原位显示一个片段，
 * 或者启动一个全新的 Activity 来显示它。
 */
public void showDetails(int index) {
    Log.v(TAG, "进入 MainActivity.showDetails(" + index + ")");
    if (isMultiPane()) {
        // 检查当前显示的是哪个片段，必要时进行替换。
        DetailsFragment details = (DetailsFragment)
                getFragmentManager().findFragmentById(R.id.details);
        if ( (details == null) ||
                (details.getShownIndex() != index) ) {
            // 创建新片段来显示此选择。
            details = DetailsFragment.newInstance(index);

            // 执行一个事务，用这个新片段替换框架内的任何现有片段。
            Log.v(TAG, "即将运行 FragmentTransaction...");
            FragmentTransaction ft
                    = getFragmentManager().beginTransaction();
            ft.setTransition(
                    FragmentTransaction.TRANSIT_FRAGMENT_FADE);
            //ft.addToBackStack("details");
            ft.replace(R.id.details, details);
            ft.commit();
        }
    } else {
        // 否则，需要启动一个新的 Activity 来显示包含所选文本的对话框片段。
        Intent intent = new Intent();
        intent.setClass(this, DetailsActivity.class);
        intent.putExtra("index", index);
        startActivity(intent);
    }
}
```

这是一个非常简单的 Activity 编写方式。为了确定多窗格模式（即，是否需要并排使用片段），只需使用设备的方向。如果是横屏模式，则为多窗格；如果是竖屏模式，则不是。辅助方法 `showDetails()` 用于在选中标题时确定如何显示文本。`index` 是标题在标题列表中的位置。如果在多窗格模式下，您将使用一个片段来显示文本。我们将这个片段称为 `DetailsFragment`，并使用一个工厂类型的方法来创建带有 `index` 的片段。`DetailsFragment` 类的有趣代码如代码清单 1-5 所示（不包括所有日志记录代码）。就像之前在 `TitlesFragment` 中做的那样，`DetailsFragment` 的各种回调函数中添加了日志记录，以便我们能够通过 LogCat 观察发生的情况。稍后我们将回到 `showDetails()` 方法。

*代码清单 1-5. **DetailsFragment** 的源代码*

```java
public class DetailsFragment extends Fragment {
    private int mIndex = 0;

    public static DetailsFragment newInstance(int index) {
        Log.v(MainActivity.TAG, "进入 DetailsFragment.newInstance(" +
                index + ")");

        DetailsFragment df = new DetailsFragment();
        // 将 index 输入作为参数提供。
        Bundle args = new Bundle();
        args.putInt("index", index);
        df.setArguments(args);
        return df;
    }

    public static DetailsFragment newInstance(Bundle bundle) {
        int index = bundle.getInt("index", 0);
        return newInstance(index);
    }

    @Override
    public void onCreate(Bundle myBundle) {
        Log.v(MainActivity.TAG,
                "进入 DetailsFragment.onCreate。Bundle 包含：");
        if(myBundle != null) {
            for(String key : myBundle.keySet()) {
                Log.v(MainActivity.TAG, " " + key);
            }
        }
        else {
            Log.v(MainActivity.TAG, " myBundle 为空");
        }
        super.onCreate(myBundle);
        mIndex = getArguments().getInt("index", 0);
    }

    public int getShownIndex() {
        return mIndex;
    }

    @Override
    public View onCreateView(LayoutInflater inflater,
                             ViewGroup container, Bundle savedInstanceState) {
        Log.v(MainActivity.TAG,
                "进入 DetailsFragment.onCreateView。container = " +
                        container);
        // 不要通过 inflater 将此片段绑定到任何东西。
        // Android 会为我们处理片段的附加。传入 container 只是为了让您
        // 了解此视图层次结构将要放置到的容器。
        View v = inflater.inflate(R.layout.details, container, false);

        TextView text1 = (TextView) v.findViewById(R.id.text1);
        text1.setText(Shakespeare.DIALOGUE[ mIndex ] );
        return v;
    }
}
```

实际上，`DetailsFragment` 类也相当简单。现在您可以看到如何实例化这个片段了。需要指出的是，您是在代码中实例化这个片段的，因为您的布局定义了您的详情片段将要放入的 `ViewGroup` 容器（一个 `FrameLayout`）。由于片段本身并没有像您的标题片段那样在 Activity 的布局 XML 中定义，因此您需要在代码中实例化您的详情片段。

要创建一个新的详情片段，您需要使用 `newInstance()` 方法。如前所述，这个工厂方法会调用默认构造函数，然后使用 `index` 的值设置参数 Bundle。一旦 `newInstance()` 运行完毕，您的详情片段就可以通过 `getArguments()` 引用参数 Bundle，在其任何回调函数中检索 `index` 的值。为了方便起见，您可以在 `onCreate()` 中将参数 Bundle 中的 `index` 值保存到 `DetailsFragment` 类的一个成员字段中。



您可能想知道为什么没有直接在`newInstance()`中设置`mIndex`值。原因是 Android 会在后台使用默认构造函数重新创建您的 Fragment，然后将其参数 Bundle 恢复为之前的状态。Android 不会使用您的`newInstance()`方法，因此确保设置`mIndex`值的唯一可靠方法是从参数 Bundle 中读取该值，并在`onCreate()`中进行设置。便捷方法`getShownIndex()`用于检索该索引的值。现在，要在详情 Fragment 中描述的最后一个方法是`onCreateView()`，它也非常简单。

`onCreateView()`的作用是返回 Fragment 的视图层次结构。请记住，根据配置的不同，您可能需要为这个 Fragment 使用各种不同的布局。因此，最常见的做法是利用布局 XML 文件来定义 Fragment 的布局。在示例应用中，您使用资源`R.layout.details`为 Fragment 指定了布局文件`details.xml`。`details.xml`的 XML 内容如**清单 1-6**所示。

*清单 1-6. 详情 Fragment 的布局文件 **details.xml***

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <ScrollView 
        android:id="@+id/scroller"
        android:layout_width="match_parent"
        android:layout_height="match_parent">
        <TextView 
            android:id="@+id/text1"
            android:layout_width="match_parent"
            android:layout_height="match_parent" />
    </ScrollView>
</LinearLayout>
```

对于您的示例应用，无论处于横屏模式还是竖屏模式，您都可以为详情 Fragment 使用完全相同的布局文件。这个布局不是为 Activity 准备的，而仅仅是让 Fragment 显示文本。由于它可以被视为默认布局，您可以将其存储在`/res/layout`目录中，即使处于横屏模式，系统也能找到并使用它。

当 Android 查找`details.xml`文件时，它会先尝试与设备配置最匹配的特定目录，但如果无法在其他位置找到`details.xml`，最终会回到`/res/layout`目录。当然，如果您希望在横屏模式下为 Fragment 使用不同的布局，您可以定义一个单独的`details.xml`布局文件，并将其存储在`/res/layout-land`目录下。请尝试使用不同的`details.xml`文件进行实验。

当详情 Fragment 的`onCreateView()`被调用时，您只需获取适当的`details.xml`布局文件，对其进行解析（inflate），并将文本设置为`Shakespeare`类中的对应文本。此处未完整展示`Shakespeare`的全部 Java 代码，但**清单 1-7** 展示了部分内容，以便您了解其实现方式。要获取完整源码，请访问项目下载文件，详见本章末尾的“参考资料”部分。

*清单 1-7. **Shakespeare.java** 的源代码*

```java
public class Shakespeare {
    public static String TITLES[] = {
        "Henry IV (1)",
        "Henry V",
        "Henry VIII",
        "Romeo and Juliet",
        "Hamlet",
        "The Merchant of Venice",
        "Othello"
    };
    public static String DIALOGUE[] = {
        "So shaken as we are, so wan with care,\n... ... ***and so on*** ..."
    };
}
```

现在，您的详情 Fragment 的视图层次结构包含了所选标题的文本。详情 Fragment 已准备就绪。现在您可以回到`MainActivity`的`showDetails()`方法来讨论`FragmentTransaction`。

## FragmentTransactions 与 Fragment 返回栈

`showDetails()`中用于引入新详情 Fragment 的代码（部分内容再次在**清单 1-8**中展示）看起来相当简单，但其中涉及了很多细节。花些时间来解释发生了什么以及为什么这样做是值得的。如果您的 Activity 处于多面板模式，您希望在标题列表旁边显示详情 Fragment。您可能已经在显示详情了，这意味着用户可能已经看到了一个详情 Fragment。无论哪种情况，资源 ID `R.id.details` 都指向 Activity 的`FrameLayout`，如**清单 1-3**所示。如果您在布局中放置了一个详情 Fragment 而未指定其他 ID，它就会拥有这个 ID。因此，要判断布局中是否存在详情 Fragment，您可以使用`findFragmentById()`询问 FragmentManager。如果框架布局为空，此方法返回`null`；否则返回当前的详情 Fragment。然后您可以决定是否需要放置一个新的详情 Fragment——无论是因为布局为空，还是因为存在其他标题的详情 Fragment。一旦决定创建并使用新的详情 Fragment，您就可以调用工厂方法来创建一个新的详情 Fragment 实例。

现在，您可以将这个新的 Fragment 放置到用户可以看到的位置。

*清单 1-8. FragmentTransaction 示例*

```java
public void showDetails(int index) {
    Log.v(TAG, "in MainActivity showDetails(" + index + ")");
    if (isMultiPane()) {
        // Check what fragment is shown, replace if needed.
        DetailsFragment details = (DetailsFragment)
                getFragmentManager().findFragmentById(R.id.details);
        if (details == null || details.getShownIndex() != index) {
            // Make new fragment to show this selection.
            details = DetailsFragment.newInstance(index);
            // Execute a transaction, replacing any existing
            // fragment with this one inside the frame.
            Log.v(TAG, "about to run FragmentTransaction...");
            FragmentTransaction ft
                    = getFragmentManager().beginTransaction();
            ft.setTransition(
                    FragmentTransaction.TRANSIT_FRAGMENT_FADE);
            //ft.addToBackStack("details");
            ft.replace(R.id.details, details);
            ft.commit();
        }
        // The rest was left out to save space.
    }
}
```

一个关键的概念需要理解：Fragment 必须生存于视图容器（也称为*视图组*）之中。`ViewGroup`类包括布局及其派生类。在 Activity 的`main.xml`布局文件中，`FrameLayout`是作为详情 Fragment 容器的理想选择。`FrameLayout`很简单，您只需要一个简单的容器来容纳 Fragment，而不需要其他类型布局带来的额外负担。`FrameLayout`就是详情 Fragment 将要放置的位置。

如果您在 Activity 的布局文件中指定了另一个`<fragment>`标签而不是`FrameLayout`，您将无法用新 Fragment 替换当前 Fragment（即交换 Fragment）。

`FragmentTransaction`正是您用于执行交换操作的对象。您告诉 Fragment 事务，您希望用新的详情 Fragment 替换框架布局中的任何内容。您原本可以通过定位详情`TextView`的资源 ID 并直接将其文本设置为新莎士比亚标题的文本来避免这一切。但关于 Fragment 的另一个方面解释了为什么您要使用`FragmentTransaction`。

如您所知，Activity 在栈中排列，随着应用不断深入，同时存在多个 Activity 的栈是很常见的。当您按下返回键时，最顶层的 Activity 消失，您将回到下面的 Activity，该 Activity 会恢复运行。这个过程可以一直持续到您回到主屏幕。



## 片段事务与动画

当 Activity 仅用于单一目的时，这种方式尚可。但现在一个 Activity 可以同时包含多个片段，并且你可以在不离开顶层 Activity 的情况下深入应用，因此 Android 确实需要将返回键堆栈的概念扩展到包含片段。实际上，片段对此的需求更为迫切。当多个片段在 Activity 中同时交互，并且需要一次性切换到多个片段的新内容时，按下返回键应使每个片段*共同*回退一步。为确保每个片段正确参与回退，需要创建并管理一个`FragmentTransaction`来协调这一过程。

请注意，Activity 中并非必须为片段提供返回堆栈。

你可以编写代码，让返回键仅在 Activity 层级工作，而完全不涉及片段层级。如果片段没有返回堆栈，按下返回键会将当前 Activity 从堆栈中弹出，并将用户返回到其下方的界面。如果你选择利用片段的返回堆栈，则需要取消清单 1-8 中 `ft.addToBackStack("details")` 这一行的注释。在此特定情况下，你将标签参数硬编码为字符串 `"details"`。该标签应是一个恰当的字符串名称，用于表示事务发生时片段的状态。该标签不一定指向某个特定片段，而是指向片段事务及事务中包含的所有片段。你可以通过代码使用标签值来查询返回堆栈，以删除条目或弹出条目。你应该为这些事务设置有意义的标签，以便后续找到相应的事务。

### 片段事务切换与动画

片段事务的一大优点在于，你可以使用切换和动画效果，从旧片段过渡到新片段。当用新的详细信息片段替换旧的时，我们可以利用片段事务切换来添加特殊效果。这能让你的应用更精致，使新旧片段的切换看起来流畅自然。

实现此目的的一种方法是使用 `setTransition()`，如清单 1-8 所示。不过，有几种不同的切换效果可供选择。在示例中你使用了淡入效果，但你也可以使用 `setCustomAnimations()` 方法描述其他特效，比如一个片段向右滑出，同时另一个从左侧滑入。自定义动画使用的是新的对象动画定义，而非旧的。旧的动画 XML 文件使用 `<translate>` 等标签，而新的 XML 文件则使用 `<objectAnimator>`。

旧的 XML 文件位于相应 Android SDK 平台目录下的 `/data/res/anim` 目录中（例如，Honeycomb 版在 `platforms/android-11` 目录下）。该目录下的 `/data/res/animator` 目录中也存在一些新的 XML 文件。你的代码可以这样写：

```
ft.setCustomAnimations(android.R.animator.fade_in, android.R.animator.fade_out);
```

这将使新片段淡入，同时旧片段淡出。第一个参数应用于进入的片段，第二个参数应用于退出的片段。请随意浏览 Android 的 animator 目录以获取更多内置动画。你需要掌握的另一重要知识点是，这些切换调用必须在 `replace()` 调用之前进行；否则，它们将不起作用。

使用对象动画为片段添加特效是实现切换的一种有趣方式。`FragmentTransaction` 上还有两个方法值得了解：`hide()` 和 `show()`。这两个方法都以片段为参数，其作用正如其名。对于片段管理器中已绑定到视图容器的片段，这些方法仅在用户界面中隐藏或显示该片段。在此过程中，片段并不会从片段管理器中被移除，但它必须与视图容器关联才能影响其可见性。

如果片段没有视图层次结构，或者其视图层次结构未绑定到显示的视图层次结构中，则这些方法将不会产生任何效果。

一旦为片段事务指定了特效，你就需要告知它你想要完成的主要工作。在你的案例中，是用新的详细信息片段替换帧布局中的任何内容。这就是 `replace()` 方法的用途。这等效于先为帧布局中已有的任何片段调用 `remove()`，然后为新的详细信息片段调用 `add()`，这意味着你也可以根据需要仅调用 `remove()` 或 `add()`。

处理片段事务时必须执行的最后一步是提交它。`commit()` 方法并不会立即执行操作，而是将工作排入计划，等待 UI 线程准备就绪时再执行。

现在你应该明白，为什么更改一个简单片段中的内容需要如此费周折。这不仅仅是为了更改文本；你可能希望在切换过程中实现特殊的图形效果。你可能还希望将切换细节保存在一个可以稍后撤销的片段事务中。最后一点可能让人困惑，下面我们来澄清一下。

这并不是严格意义上的“事务”。当你从返回堆栈中弹出片段事务时，并不会撤销可能已发生的所有数据更改。例如，如果你的 Activity 内部数据在你于返回堆栈上创建片段事务时发生了变化，按下返回键并不会让 Activity 的数据更改恢复到之前的值。你仅仅是按照与 Activity 相同的方式，在用户界面视图中逐级返回，只不过这里针对的是片段。由于片段保存和恢复的方式，从保存状态恢复的片段的内部状态，将取决于你随片段保存了哪些值以及你如何管理这些值的恢复。因此，你的片段可能看起来和之前一样，但你的 Activity 不会，除非你在恢复片段的同时采取措施恢复 Activity 的状态。

在你的示例中，你只操作一个视图容器并引入一个详细信息片段。如果你的用户界面更复杂，你可以在片段事务中操作其他片段。实际上，你正在执行的是：开始事务，用新的详细信息片段替换详细信息帧布局中的任何现有片段，指定淡入动画，然后提交事务。你注释掉了将事务添加到返回堆栈的那部分代码，但当然可以取消注释以参与返回堆栈。

### FragmentManager

`FragmentManager` 是一个负责管理属于某个 Activity 的所有片段的组件。这包括返回堆栈上的片段以及可能只是暂时存在的片段。下面我们来详细说明。



片段只能在活动的上下文中创建。这可以通过加载活动的布局 XML 文件，或者通过使用如代码清单 1-1 中的代码直接实例化来实现。当通过代码实例化时，片段通常通过片段事务附加到活动。无论哪种情况，都会使用 `FragmentManager` 类来访问和管理这些活动中的片段。

您可以在活动或已附加的片段上使用 `getFragmentManager()` 方法来获取片段管理器。您从代码清单 1-8 中看到，片段管理器就是获取片段事务的地方。除了获取片段事务，您还可以通过片段的 ID、标签或组合键及键值对来获取片段。如果片段是从 XML 加载的，其 ID 将是片段的资源 ID；如果片段是通过片段事务放入视图中的，其 ID 将是容器的资源 ID。片段的标签是一个 `String`，您可以在片段的 XML 定义中分配，也可以在通过片段事务将片段放入视图时分配。通过组合键及键值对来检索片段的方法仅适用于使用 `putFragment()` 方法持久化保存的片段。

用于获取片段的 getter 方法包括 `findFragmentById()`、`findFragmentByTag()` 和 `getFragment()`。`getFragment()` 方法会与 `putFragment()` 方法配合使用，后者也需要一个组合键、一个键以及待保存的片段。该组合键很可能就是 `savedState` 组合键，而 `putFragment()` 将在 `onSaveInstanceState()` 回调中使用，以保存当前活动（或另一个片段）的状态。`getFragment()` 方法可能会在 `onCreate()` 中调用，以对应 `putFragment()`，不过对于片段来说，如前所述，该组合键也适用于其他回调方法。

显然，您无法对尚未附加到活动的片段使用 `getFragmentManager()` 方法。但同样，您可以将片段附加到活动，而无需让用户立即看到它。如果您这样做，应该为片段关联一个 `String` 标签，以便将来能够找到它。您很可能会使用 `FragmentTransaction` 的以下方法来实现：

```
public FragmentTransaction add (Fragment fragment, String tag)
```

事实上，您可以拥有一个不显示视图层次结构的片段。这样做可能是为了将某些逻辑封装在一起，使其能够附加到活动，同时仍然保留相对于活动生命周期和其他片段的一定自主性。当活动因设备配置更改而经历重新创建周期时，这个非 UI 片段可以在活动消失并重新出现时基本保持完整。这对于 `setRetainInstance()` 选项来说是一个很好的应用场景。

片段的回退栈也属于片段管理器的管辖范围。片段事务用于将片段放入回退栈，而片段管理器则可以将片段从回退栈中移除。这通常通过片段的 ID 或标签来完成，但也可以基于回退栈中的位置，或者仅仅是弹出栈顶的片段。

最后，片段管理器还提供了一些用于调试功能的方法，例如使用 `enableDebugLogging()` 开启向 LogCat 输出调试信息，或使用 `dump()` 将片段管理器的当前状态输出到流中。请注意，您在代码清单 1-4 的活动 `onCreate()` 方法中开启了片段管理器调试。

## 引用片段时的注意事项

现在，让我们重新审视之前关于片段生命周期以及参数和已保存状态组合键的讨论。Android 可能在不同的时间点保存您的某个片段。这意味着，当您的应用程序想要检索该片段时，它可能不在内存中。因此，我们提醒您**不要**认为对片段的变量引用会长时间有效。如果使用片段事务在容器视图中替换片段，那么对旧片段的任何引用现在都指向一个可能位于回退栈中的片段。或者，在应用程序配置更改（例如屏幕旋转）期间，片段可能会从活动的视图层次结构中分离。请务必小心。

如果您要持有对片段的引用，请注意它何时可能被保存；当您需要再次找到它时，应使用片段管理器的某个 getter 方法。如果您想持有一个片段引用，例如当活动经历配置更改时，您可以使用 `putFragment()` 方法，并传入适当的组合键。对于活动和片段而言，适当的组合键就是 `onSaveInstanceState()` 中使用的、并会在 `onCreate()`（对于片段，则是片段生命周期中的其他早期回调）中重新出现的 `savedState` 组合键。您可能永远不会将对片段的直接引用存储到片段的参数组合键中；如果您有此想法，请务必先三思。

获取特定片段的另一种方式是使用已知的标签或已知的 ID 进行查询。先前描述的 getter 方法允许通过这种方式从片段管理器中检索片段，这意味着您可以选择仅记住片段的标签或 ID，以便使用这些值之一从片段管理器中获取它，而不是使用 `putFragment()` 和 `getFragment()`。

## 保存片段状态

Android 3.2 中引入了另一个有趣的类：`Fragment.SavedState`。使用 `FragmentManager` 的 `saveFragmentInstanceState()` 方法，您可以向此方法传递一个片段，它会返回一个代表该片段状态的对象。然后，您可以使用 `Fragment` 的 `setInitialSavedState()` 方法在初始化片段时使用该对象。第 2 章将对此进行更详细的讨论。

## ListFragments 与 <fragment>

要使您的示例应用程序完整，还需要介绍一些内容。首先是 `TitlesFragment` 类。这是通过您主活动的 `main.xml` 文件创建的类。`<fragment>` 标签充当了该片段位置的占位符，并且没有定义该片段的视图层次结构。`TitlesFragment` 的代码关键部分在代码清单 1-9 中。完整代码请参考源代码文件。`TitlesFragment` 用于显示应用程序的标题列表。

*代码清单 1-9. **TitlesFragment** Java 代码*

```
public class TitlesFragment extends ListFragment {

private MainActivity myActivity = null;

int mCurCheckPosition = 0;

@Override

public void onAttach(Activity myActivity) {

Log.v(MainActivity.TAG,

"in TitlesFragment onAttach; activity is: " + myActivity); super.onAttach(myActivity);

this.myActivity = (MainActivity)myActivity;

}

@Override

public void onActivityCreated(Bundle savedState) {

Log.v(MainActivity.TAG,

"in TitlesFragment onActivityCreated. savedState contains:"); if(savedState != null) {

for(String key : savedState.keySet()) {

Log.v(MainActivity.TAG, " " + key);

}

}

else {

Log.v(MainActivity.TAG, " savedState is null");

}

super.onActivityCreated(savedState);

// 用你的静态标题数组填充列表。

setListAdapter(new ArrayAdapter<String>(getActivity(),

android.R.layout.simple_list_item_1,

Shakespeare.TITLES));
```



```java
if (savedState != null) {
    // 为已选中的位置恢复最后保存的状态。
    mCurCheckPosition = savedState.getInt("curChoice", 0);
}

// 获取 ListFragment 的 ListView 并更新它
ListView lv = getListView();
lv.setChoiceMode(ListView.CHOICE_MODE_SINGLE);
lv.setSelection(mCurCheckPosition);

// Activity 已创建，Fragment 可用
// 开始填充详情 fragment
myActivity.showDetails(mCurCheckPosition);
```

[www.it-ebooks.info](http://www.it-ebooks.info/)

